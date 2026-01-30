# Design: Collection MVP

## Context

BookSync est une application desktop PySide6/QML pour Raspberry Pi avec écran tactile. L'application suit une Clean Architecture (Hexagonale) avec les couches:
- **Domain** — Entités métier et ports/interfaces
- **Application** — Services et cas d'usage
- **Infrastructure** — Implémentations concrètes (API, cache)
- **Presentation** — Controllers Qt et QML

L'API Mangacollec est la source de vérité. Le cache local SQLite permet les performances et le mode offline (lecture seule).

## Goals / Non-Goals

### Goals
- Afficher la collection de l'utilisateur (volumes possédés)
- Permettre de retirer un tome de la collection
- Afficher la progression par série (ex: "3/10 tomes")
- Supporter le tri (date d'ajout, alphabétique)
- Fonctionner avec cache local pour les performances
- Interface tactile optimisée (1280×720)

### Non-Goals
- Ajout de tomes (nécessite SearchService)
- Fiches détail (autre proposal)
- Authentification OAuth complète (mode simplifié dev)
- Mode offline écriture

## Decisions

### 1. Architecture des couches

**Decision**: Suivre strictement la Clean Architecture documentée dans PRD.md section 6.2

```
presentation/ (Controllers Qt + QML)
      ↓
application/ (CollectionService)
      ↓
domain/ (Entities + Ports)
      ↓
infrastructure/ (MangacollecApi + SQLiteCache)
```

**Rationale**: Architecture documentée et validée, séparation claire des responsabilités.

### 2. Modèle de données collection

**Decision**: Stocker uniquement les `possessions` (relation user↔volume) en cache, les volumes complets sont récupérés via jointure ou lazy loading.

**Alternatives considérées**:
- Stocker volumes complets avec possessions → Duplication de données
- Requête API à chaque affichage → Lent, pas de mode offline

**Rationale**: Économise l'espace cache, permet de rafraîchir les volumes indépendamment.

### 3. QAbstractListModel pour les listes

**Decision**: Utiliser `QAbstractListModel` avec roles personnalisés pour exposer les données au QML.

```python
class VolumeListModel(QAbstractListModel):
    IdRole = Qt.UserRole + 1
    TitleRole = Qt.UserRole + 2
    # ...
```

**Rationale**: Pattern Qt standard, performant pour les longues listes, intégration native avec ListView QML.

### 4. Gestion async avec Qt

**Decision**: Utiliser `asyncio.create_task()` dans les controllers pour les opérations async, avec signaux Qt pour notifier le QML.

**Alternatives considérées**:
- QThread → Plus complexe, moins Pythonic
- Synchrone bloquant → Freeze l'UI

**Rationale**: PySide6 supporte bien asyncio, pattern simple et maintenable.

### 5. Tri et filtrage

**Decision**: Tri côté Python dans le service, pas en SQL ni en QML.

**Rationale**:
- Plus flexible (tri multi-critères)
- Collection typiquement <5000 items (performant en mémoire)
- Évite la complexité des requêtes SQL dynamiques

## Risks / Trade-offs

| Risque | Impact | Mitigation |
|--------|--------|------------|
| Performance sur RPi avec grande collection | UI lente | Pagination virtuelle dans ListView, chargement lazy des images |
| Cache désynchronisé avec API | Données obsolètes | TTL de 5 minutes sur possessions, bouton refresh manuel |
| Complexité async/signals | Bugs race conditions | Tests unitaires, pattern single-writer |

## Migration Plan

N/A — Projet vierge, pas de migration nécessaire.

## Open Questions

1. **Format des images couvertures**: Télécharger et cacher localement ou utiliser URL directement ? → Décision: URL directe pour MVP, cache images en V2.

2. **Gestion erreurs réseau**: Modal bloquant ou toast notification ? → Décision: Toast non-bloquant avec action retry.
