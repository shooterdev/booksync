# Docker — BookSync

Guide complet pour la configuration et le déploiement Docker des services BookSync.

---

## Table des matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Images Docker](#2-images-docker)
3. [Services](#3-services)
4. [Commandes utiles](#4-commandes-utiles)
5. [Configuration réseau](#5-configuration-réseau)
6. [Variables d'environnement](#6-variables-denvironnement)
7. [Volumes et persistance](#7-volumes-et-persistance)
8. [Déploiement production](#8-déploiement-production)

---

## 1. Vue d'ensemble

### 1.1 Architecture des conteneurs

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          DOCKER NETWORK                                 │
│                         (booksync-network)                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   ┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐       │
│   │   Auth API      │   │   Data API      │   │  Prediction API │       │
│   │   Port: 8000    │   │   Port: 8001    │   │   Port: 8002    │       │
│   │   Python 3.12   │   │   Python 3.12   │   │   Python 3.12   │       │
│   └────────┬────────┘   └────────┬────────┘   └────────┬────────┘       │
│            │                     │                     │                │
│            └─────────────────────┼─────────────────────┘                │
│                                  │                                      │
│                                  ▼                                      │
│                    ┌─────────────────────────┐                          │
│                    │      PostgreSQL 17      │                          │
│                    │    booksync-postgres    │                          │
│                    │      Port: 5432         │                          │
│                    │   + pgvector extension  │                          │
│                    │                         │                          │
│                    │  Base unique partagée   │                          │
│                    │   par tous les services │                          │
│                    └─────────────────────────┘                          │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 1.2 Résumé des services

| Service        | Image                            | Port | Base Python | Dépendances |
|----------------|----------------------------------|------|-------------|-------------|
| Auth API       | `booksync-api-auth:latest`       | 8000 | 3.12-slim   | PostgreSQL  |
| Data API       | `booksync-api-data:latest`       | 8001 | 3.12-slim   | PostgreSQL  |
| Prediction API | `booksync-api-prediction:latest` | 8002 | 3.12-slim   | PostgreSQL  |
| PostgreSQL     | `pgvector/pgvector:pg17`         | 5432 | —           | —           |

> **Note** : Tous les services partagent la même instance PostgreSQL avec l'extension `pgvector` activée.

---

## 2. Images Docker

### 2.0 Build multi-architecture (arm64 + amd64)

Les images sont construites pour **arm64** (Raspberry Pi) et **amd64** (serveurs x86).

```bash
# Créer un builder multi-plateforme (une seule fois)
docker buildx create --name booksync-builder --use

# Vérifier les plateformes disponibles
docker buildx inspect --bootstrap
```

### 2.1 Auth API

**Nom de l'image**: `booksync-api-auth`

```bash
# Build local (architecture courante)
docker build -t booksync-api-auth:latest ./booksync_api_auth

# Build multi-architecture (arm64 + amd64)
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t booksync-api-auth:latest \
  -t booksync-api-auth:1.0.0 \
  --push \
  ./booksync_api_auth

# Build multi-arch sans push (load local)
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t booksync-api-auth:latest \
  --output type=docker \
  ./booksync_api_auth
```

**Spécifications**:
- Base: `python:3.12-slim`
- Port exposé: `8000`
- Architectures: `linux/amd64`, `linux/arm64`
- Entrée: `uvicorn booksync_api_auth.main:app`

### 2.2 Data API

**Nom de l'image**: `booksync-api-data`

```bash
# Build local (architecture courante)
docker build -t booksync-api-data:latest ./booksync_api_data

# Build multi-architecture (arm64 + amd64)
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t booksync-api-data:latest \
  -t booksync-api-data:1.0.0 \
  --push \
  ./booksync_api_data
```

**Spécifications**:
- Base: `python:3.12-slim`
- Port exposé: `8001`
- Architectures: `linux/amd64`, `linux/arm64`
- Entrée: `uvicorn booksync_api_data.main:app`

### 2.3 Prediction API

**Nom de l'image**: `booksync-api-prediction`

```bash
# Build local (architecture courante)
docker build -t booksync-api-prediction:latest ./booksync_api_prediction

# Build multi-architecture (arm64 + amd64)
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t booksync-api-prediction:latest \
  -t booksync-api-prediction:1.0.0 \
  --push \
  ./booksync_api_prediction
```

**Spécifications**:
- Base: `python:3.12-slim`
- Port exposé: `8002`
- Architectures: `linux/amd64`, `linux/arm64`
- Entrée: `uvicorn booksync_api_prediction.main:app`
- Extension requise: `pgvector`

### 2.4 Build de toutes les images (script)

```bash
#!/bin/bash
# scripts/build-all.sh

PLATFORMS="linux/amd64,linux/arm64"
VERSION=${1:-latest}

echo "Building all images for $PLATFORMS..."

# Auth API
docker buildx build \
  --platform $PLATFORMS \
  -t booksync-api-auth:$VERSION \
  --push \
  ./booksync_api_auth

# Data API
docker buildx build \
  --platform $PLATFORMS \
  -t booksync-api-data:$VERSION \
  --push \
  ./booksync_api_data

# Prediction API
docker buildx build \
  --platform $PLATFORMS \
  -t booksync-api-prediction:$VERSION \
  --push \
  ./booksync_api_prediction

echo "All images built and pushed!"
```

---

## 3. Services

### 3.1 Auth API — Service d'authentification

**Conteneur**: `booksync_auth_api`

```bash
# Lancer avec Docker run (connecté à la BDD partagée)
docker run -d \
  --name booksync_auth_api \
  --network booksync-network \
  -p 8000:8000 \
  -e DATABASE_URL="postgresql+asyncpg://booksync_auth:auth_password@booksync-postgres:5432/booksync" \
  -e JWT_SECRET_KEY="your-secret-key" \
  -e MANGACOLLEC_CLIENT_ID="your-client-id" \
  -e MANGACOLLEC_CLIENT_SECRET="your-client-secret" \
  booksync-api-auth:latest

# Avec docker-compose (développement)
cd booksync_api_auth && docker-compose up -d
```

**Endpoints de santé**:
- `GET /health` — Vérification santé
- `GET /docs` — Documentation Swagger

### 3.2 Data API — Service de données supplémentaires

**Conteneur**: `booksync-api-data`

```bash
# Lancer avec Docker run (même BDD que les autres services)
docker run -d \
  --name booksync-api-data \
  --network booksync-network \
  -p 8001:8001 \
  -e DATABASE_URL="postgresql+asyncpg://booksync_data:data_password@booksync-postgres:5432/booksync" \
  booksync-api-data:latest

# Avec docker-compose (développement)
cd booksync_api_data && docker-compose up -d
```

**Endpoints de santé**:
- `GET /health` — Vérification santé
- `GET /docs` — Documentation Swagger

### 3.3 Prediction API — Service de recommandations ML

**Conteneur**: `booksync-api-prediction`

```bash
# Lancer avec Docker run (même BDD que les autres services)
docker run -d \
  --name booksync-api-prediction \
  --network booksync-network \
  -p 8002:8002 \
  -e DATABASE_URL="postgresql+asyncpg://booksync_prediction:prediction_password@booksync-postgres:5432/booksync" \
  -e EMBEDDING_MODEL="all-MiniLM-L6-v2" \
  -e VECTOR_DIMENSION=384 \
  booksync-api-prediction:latest

# Avec docker-compose (développement)
cd booksync_api_prediction && docker-compose up -d
```

**Endpoints de santé**:
- `GET /health` — Vérification santé
- `GET /docs` — Documentation Swagger

**Prérequis**: PostgreSQL avec extension `pgvector` installée.

### 3.4 PostgreSQL — Base de données partagée

**Conteneur**: `booksync-postgres`

Tous les services (Auth, Data, Prediction) se connectent à cette unique instance PostgreSQL.

```bash
# Lancer PostgreSQL avec pgvector
docker run -d \
  --name booksync-postgres \
  --network booksync-network \
  -p 5432:5432 \
  -e POSTGRES_USER=booksync \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=booksync \
  -v booksync_pgdata:/var/lib/postgresql/data \
  pgvector/pgvector:pg17

# Initialiser l'extension pgvector (requise pour Prediction API)
docker exec -it booksync-postgres psql -U booksync -d booksync -c "CREATE EXTENSION IF NOT EXISTS vector;"
```

**Connexion depuis les services** (chaque service a son propre utilisateur) :

| Service        | Utilisateur           | URL de connexion                                                               |
|----------------|-----------------------|--------------------------------------------------------------------------------|
| Auth API       | `booksync_auth`       | `postgresql+asyncpg://booksync_auth:***@booksync-postgres:5432/booksync`       |
| Data API       | `booksync_data`       | `postgresql+asyncpg://booksync_data:***@booksync-postgres:5432/booksync`       |
| Prediction API | `booksync_prediction` | `postgresql+asyncpg://booksync_prediction:***@booksync-postgres:5432/booksync` |

> **Sécurité** : Chaque service dispose de son propre utilisateur PostgreSQL avec mot de passe distinct pour une meilleure isolation.

---

## 4. Commandes utiles

### 4.1 Gestion des conteneurs

```bash
# Voir tous les conteneurs BookSync
docker ps -a --filter "name=booksync"

# Arrêter tous les conteneurs BookSync
docker stop $(docker ps -q --filter "name=booksync")

# Supprimer tous les conteneurs BookSync
docker rm $(docker ps -aq --filter "name=booksync")

# Redémarrer un service
docker restart booksync_auth_api
```

### 4.2 Logs et debugging

```bash
# Voir les logs d'un service
docker logs booksync_auth_api

# Suivre les logs en temps réel
docker logs -f booksync_auth_api

# Logs avec timestamp
docker logs -t booksync_auth_api

# Dernières 100 lignes
docker logs --tail 100 booksync_auth_api

# Shell interactif dans un conteneur
docker exec -it booksync_auth_api /bin/bash
```

### 4.3 Build et images

```bash
# Build local (architecture courante)
docker build -t booksync-api-auth:latest ./booksync_api_auth && \
docker build -t booksync-api-data:latest ./booksync_api_data && \
docker build -t booksync-api-prediction:latest ./booksync_api_prediction

# Build multi-architecture (arm64 + amd64)
./scripts/build-all.sh 1.0.0

# Lister les images BookSync
docker images | grep booksync

# Voir les architectures supportées par une image
docker buildx imagetools inspect booksync-api-auth:latest

# Supprimer les images non utilisées
docker image prune -a

# Inspecter une image
docker inspect booksync-api-auth:latest
```

### 4.4 Docker Compose

```bash
# Démarrer tous les services (depuis la racine)
docker-compose up -d

# Démarrer avec rebuild
docker-compose up -d --build

# Voir le statut
docker-compose ps

# Arrêter tous les services
docker-compose down

# Arrêter et supprimer les volumes
docker-compose down -v

# Voir les logs de tous les services
docker-compose logs -f

# Logs d'un service spécifique
docker-compose logs -f auth-api
```

### 4.5 Réseau

```bash
# Créer le réseau BookSync
docker network create booksync-network

# Lister les réseaux
docker network ls

# Inspecter le réseau
docker network inspect booksync-network

# Connecter un conteneur au réseau
docker network connect booksync-network booksync_auth_api
```

### 4.6 Volumes

```bash
# Lister les volumes
docker volume ls

# Créer un volume
docker volume create booksync_pgdata

# Inspecter un volume
docker volume inspect booksync_pgdata

# Supprimer un volume
docker volume rm booksync_pgdata

# Supprimer les volumes non utilisés
docker volume prune
```

---

## 5. Configuration réseau

### 5.1 Ports exposés

| Service         | Port interne | Port hôte | Protocole |
|-----------------|--------------|-----------|-----------|
| Auth API        | 8000         | 8000      | HTTP      |
| Data API        | 8001         | 8001      | HTTP      |
| Prediction API  | 8002         | 8002      | HTTP      |
| PostgreSQL      | 5432         | 5432      | TCP       |

### 5.2 Communication inter-services

Dans Docker Compose, les services communiquent via leur nom de service :

```yaml
# Exemple dans docker-compose.yml
services:
  auth-api:
    environment:
      - DATABASE_URL=postgresql+asyncpg://postgres:password@postgres:5432/booksync

  postgres:
    image: postgres:17-alpine
```

### 5.3 Accès depuis l'hôte

Pour accéder aux services depuis la machine hôte :

```bash
# Auth API
curl http://localhost:8000/health

# Data API
curl http://localhost:8001/health

# Prediction API
curl http://localhost:8002/health
```

---

## 6. Variables d'environnement

### 6.1 Auth API

| Variable                    | Description                           | Défaut                  |
|-----------------------------|---------------------------------------|-------------------------|
| `DATABASE_URL`              | URL PostgreSQL                        | Requis                  |
| `JWT_SECRET_KEY`            | Clé secrète JWT                       | Requis                  |
| `JWT_ALGORITHM`             | Algorithme JWT                        | `HS256`                 |
| `JWT_EXPIRE_MINUTES`        | Expiration token (0=jamais)           | `0`                     |
| `MANGACOLLEC_CLIENT_ID`     | Client ID OAuth2 Mangacollec          | Requis                  |
| `MANGACOLLEC_CLIENT_SECRET` | Client Secret OAuth2 Mangacollec      | Requis                  |
| `HOST`                      | Adresse d'écoute                      | `0.0.0.0`               |
| `PORT`                      | Port d'écoute                         | `8000`                  |
| `DEBUG`                     | Mode debug                            | `false`                 |
| `BCRYPT_ROUNDS`             | Rounds bcrypt                         | `12`                    |

### 6.2 Data API

| Variable         | Description              | Défaut        |
|------------------|--------------------------|---------------|
| `DATABASE_URL`   | URL PostgreSQL           | Requis        |
| `APP_ENV`        | Environnement            | `development` |
| `APP_DEBUG`      | Mode debug               | `true`        |
| `APP_SECRET_KEY` | Clé secrète application  | Requis        |

> **Note** : Utilise la même `DATABASE_URL` que les autres services pour se connecter à la BDD partagée.

### 6.3 Prediction API

| Variable               | Description                      | Défaut              |
|------------------------|----------------------------------|---------------------|
| `DATABASE_URL`         | URL PostgreSQL                   | Requis              |
| `DATABASE_ECHO`        | Afficher requêtes SQL            | `false`             |
| `VECTOR_DIMENSION`     | Dimension des vecteurs           | `384`               |
| `SIMILARITY_THRESHOLD` | Seuil de similarité              | `0.7`               |
| `EMBEDDING_MODEL`      | Modèle sentence-transformers     | `all-MiniLM-L6-v2`  |
| `EMBEDDING_BATCH_SIZE` | Taille batch embeddings          | `32`                |
| `DEVICE`               | Device ML (cpu/cuda)             | `cpu`               |
| `HOST`                 | Adresse d'écoute                 | `0.0.0.0`           |
| `PORT`                 | Port d'écoute                    | `8002`              |

---

## 7. Volumes et persistance

### 7.1 Volume PostgreSQL

```yaml
volumes:
  booksync_pgdata:    # Base de données partagée (Auth + Data + Prediction)
```

> **Note** : Une seule instance PostgreSQL avec pgvector stocke toutes les données des services.

### 7.2 Sauvegarde des données

```bash
# Sauvegarder la base de données
docker exec booksync-postgres pg_dump -U booksync booksync > backup_$(date +%Y%m%d).sql

# Restaurer la base de données
cat backup_20240115.sql | docker exec -i booksync-postgres psql -U booksync booksync
```

---

## 8. Déploiement production

### 8.1 Structure de déploiement

```
/srv/booksync/
├── docker-compose.yml        # Orchestration principale
├── .env                      # Variables d'environnement
├── auth-api/
│   └── Dockerfile
├── data-api/
│   └── Dockerfile
├── prediction-api/
│   └── Dockerfile
├── postgres/
│   └── data/                 # Volume données PostgreSQL
├── nginx/
│   └── nginx.conf            # Configuration reverse proxy
└── scripts/
    ├── backup.sh             # Script de sauvegarde
    └── deploy.sh             # Script de déploiement
```

### 8.2 Docker Compose de production

```yaml
# docker-compose.prod.yml

services:
  postgres:
    image: pgvector/pgvector:pg17
    container_name: booksync-postgres
    restart: always
    environment:
      POSTGRES_USER: ${DB_ADMIN_USER}
      POSTGRES_PASSWORD: ${DB_ADMIN_PASSWORD}
      POSTGRES_DB: booksync_dev
    volumes:
      - booksync_pgdata:/var/lib/postgresql/data
      - ./scripts/init-db.sql:/docker-entrypoint-initdb.d/01-init-db.sql
    networks:
      - booksync-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_ADMIN_USER}"]
      interval: 10s
      timeout: 5s
      retries: 5

  # ============================================
  # SERVICES API
  # Chaque service a son propre utilisateur BDD
  # ============================================
  auth-api:
    image: booksync-api-auth:latest
    container_name: booksync_auth_api
    restart: always
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql+asyncpg://${DB_AUTH_USER}:${DB_AUTH_PASSWORD}@postgres:5432/booksync
      JWT_SECRET_KEY: ${JWT_SECRET_KEY}
      MANGACOLLEC_CLIENT_ID: ${MANGACOLLEC_CLIENT_ID}
      MANGACOLLEC_CLIENT_SECRET: ${MANGACOLLEC_CLIENT_SECRET}
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - booksync-network

  data-api:
    image: booksync-api-data:latest
    container_name: booksync-api-data
    restart: always
    ports:
      - "8001:8001"
    environment:
      DATABASE_URL: postgresql+asyncpg://${DB_DATA_USER}:${DB_DATA_PASSWORD}@postgres:5432/booksync
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - booksync-network

  prediction-api:
    image: booksync-api-prediction:latest
    container_name: booksync-api-prediction
    restart: always
    ports:
      - "8002:8002"
    environment:
      DATABASE_URL: postgresql+asyncpg://${DB_PREDICTION_USER}:${DB_PREDICTION_PASSWORD}@postgres:5432/booksync
      EMBEDDING_MODEL: all-MiniLM-L6-v2
      DEVICE: cpu
    depends_on:
      postgres:
        condition: service_healthy
    networks:
      - booksync-network

networks:
  booksync-network:
    driver: bridge

volumes:
  booksync_pgdata:  # Volume unique pour la BDD partagée
```

> **Sécurité** : Chaque service utilise son propre utilisateur PostgreSQL 
> (`booksync_auth`, `booksync_data`, `booksync_prediction`) avec des mots de passe distincts.

### 8.3 Commandes de déploiement

```bash
# Déploiement initial
cd /srv/booksync
docker-compose -f docker-compose.prod.yml up -d

# Mise à jour d'un service
docker-compose -f docker-compose.prod.yml pull auth-api
docker-compose -f docker-compose.prod.yml up -d auth-api

# Mise à jour complète
docker-compose -f docker-compose.prod.yml pull
docker-compose -f docker-compose.prod.yml up -d

# Rollback
docker-compose -f docker-compose.prod.yml stop auth-api
docker run -d --name booksync_auth_api booksync-api-auth:previous-version
```

### 8.4 Monitoring

```bash
# État des services
docker-compose -f docker-compose.prod.yml ps

# Santé des conteneurs
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# Utilisation des ressources
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

---

## Annexes

### A. Fichier .env exemple

```env
# ============================================
# PostgreSQL Admin (création BDD)
# ============================================
DB_ADMIN_USER=postgres
DB_ADMIN_PASSWORD=admin-secure-password

# ============================================
# Utilisateurs par service (sécurité)
# ============================================
# Auth API
DB_AUTH_USER=booksync_auth
DB_AUTH_PASSWORD=auth-secure-password

# Data API
DB_DATA_USER=booksync_data
DB_DATA_PASSWORD=data-secure-password

# Prediction API
DB_PREDICTION_USER=booksync_prediction
DB_PREDICTION_PASSWORD=prediction-secure-password

# ============================================
# JWT & OAuth2
# ============================================
JWT_SECRET_KEY=your-super-secret-jwt-key

# Mangacollec OAuth2
MANGACOLLEC_CLIENT_ID=your-client-id
MANGACOLLEC_CLIENT_SECRET=your-client-secret

# ============================================
# Environment
# ============================================
ENVIRONMENT=production
DEBUG=false
```

### B. Script d'initialisation PostgreSQL

Créer le fichier `scripts/init-db.sql` :

```sql
-- Activation de pgvector
CREATE EXTENSION IF NOT EXISTS vector;

-- Création des utilisateurs par service
CREATE USER booksync_auth WITH PASSWORD 'auth-secure-password';
CREATE USER booksync_data WITH PASSWORD 'data-secure-password';
CREATE USER booksync_prediction WITH PASSWORD 'prediction-secure-password';

-- Attribution des privilèges
GRANT CONNECT ON DATABASE booksync TO booksync_auth;
GRANT CONNECT ON DATABASE booksync TO booksync_data;
GRANT CONNECT ON DATABASE booksync TO booksync_prediction;

-- Privilèges sur le schéma public
GRANT USAGE ON SCHEMA public TO booksync_auth;
GRANT USAGE ON SCHEMA public TO booksync_data;
GRANT USAGE ON SCHEMA public TO booksync_prediction;

-- Privilèges sur les tables (à adapter selon les besoins)
-- Auth API : tables users, sessions, credentials
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO booksync_auth;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO booksync_auth;

-- Data API : tables volume_extra
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO booksync_data;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO booksync_data;

-- Prediction API : tables embeddings, recommendations
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO booksync_prediction;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO booksync_prediction;

-- Privilèges par défaut pour les futures tables
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO booksync_auth;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO booksync_data;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON TABLES TO booksync_prediction;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO booksync_auth;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO booksync_data;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL ON SEQUENCES TO booksync_prediction;
```

### B. Troubleshooting

| Problème                          | Solution                                                |
|-----------------------------------|---------------------------------------------------------|
| Container ne démarre pas          | `docker logs <container>` pour voir les erreurs         |
| Port déjà utilisé                 | `lsof -i :8000` puis `kill -9 <PID>`                   |
| Base de données inaccessible      | Vérifier que PostgreSQL est démarré et les credentials  |
| pgvector non trouvé               | `CREATE EXTENSION IF NOT EXISTS vector;`                |
| Mémoire insuffisante              | Augmenter Docker memory limit ou utiliser `DEVICE=cpu`  |
| Network unreachable               | Vérifier que les conteneurs sont sur le même réseau     |
