# API Mangacollec — Documentation

Documentation de l'API externe Mangacollec utilisée par BookSync.

---

## Table des matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Authentification](#2-authentification)
3. [Index des ressources](#3-index-des-ressources)
4. [Endpoints par page](#4-endpoints-par-page)
5. [Gestion des erreurs](#5-gestion-des-erreurs)
6. [Notes techniques](#6-notes-techniques)

---

## 1. Vue d'ensemble

| Aspect       | Valeur                             |
|--------------|------------------------------------|
| URL de base  | `https://api.mangacollec.com`      |
| Format       | JSON                               |
| Encoding     | UTF-8                              |
| Versioning   | Préfixe `/v1` ou `/v2` dans l'URL  |

---

## 2. Authentification

| Méthode          | OAuth2 (Client Credentials)                     |
|------------------|-------------------------------------------------|
| Client ID        | Voir variables d'environnement                  |
| Client Secret    | Voir variables d'environnement                  |
| Header           | `Authorization: Bearer {access_token}`          |
| Token Endpoint   | <!-- TODO: À compléter -->                      |
| Refresh          | Géré par Auth API locale                        |

---

## 3. Index des ressources

| Ressource    | Fichier                                         | Description                          |
|--------------|-------------------------------------------------|--------------------------------------|
| Volumes      | [volumes.md](api/mangacollec/volumes.md)        | Tomes, nouveautés, détails           |
| Séries       | [series.md](api/mangacollec/series.md)          | Séries manga                         |
| Éditions     | [editions.md](api/mangacollec/editions.md)      | Éditions par éditeur                 |
| Éditeurs     | [publishers.md](api/mangacollec/publishers.md)  | Liste et détails éditeurs            |
| Auteurs      | [authors.md](api/mangacollec/authors.md)        | Auteurs et leurs œuvres              |
| Métiers      | [jobs.md](api/mangacollec/jobs.md)              | Métiers (scénariste, dessinateur...) |
| Utilisateurs | [users.md](api/mangacollec/users.md)            | Profil utilisateur                   |
| Collection   | [collection.md](api/mangacollec/collection.md)  | Collection et possessions            |
| Panier       | [cart.md](api/mangacollec/cart.md)              | Panier d'achats                      |
| Planning     | [planning.md](api/mangacollec/planning.md)      | Sorties à venir                      |
| Offres       | [offers.md](api/mangacollec/offers.md)          | Offres marchands (BDfugue, Amazon)   |
| Genres       | [kinds.md](api/mangacollec/kinds.md)            | Genres/catégories                    |

---

## 4. Endpoints par page

Voir [COMMON.md § Endpoints par page](COMMON.md#endpoints-par-page-authentifié) pour la liste complète des endpoints 
utilisés par chaque page de l'application.

---

## 5. Gestion des erreurs

### Codes HTTP

| Code | Signification                              |
|------|--------------------------------------------|
| 200  | Succès                                     |
| 201  | Créé                                       |
| 204  | Pas de contenu (suppression réussie)       |
| 400  | Requête invalide                           |
| 401  | Non authentifié                            |
| 403  | Accès interdit                             |
| 404  | Ressource non trouvée                      |
| 422  | Entité non traitable (validation)          |
| 429  | Trop de requêtes (rate limiting)           |
| 500  | Erreur serveur                             |

### Format d'erreur

<!-- TODO: À compléter avec le format réel -->

```json
{

}
```

---

## 6. Notes techniques

### Cache

Voir [COMMON.md § Cache local SQLite](COMMON.md#53-cache-local-sqlite) pour les durées de validité par type de données.
