# Design: Page News (Nouveautés)

## Context

La page News est la première page à afficher des données combinées (volumes + coffrets) provenant d'un endpoint unique (`/v2/volumes/news`). Elle introduit le pattern de page "catalogue" différent de la page Collection qui affiche des données utilisateur.

### Contraintes

- Données mixtes : volumes classiques + coffrets dans la même grille
- Dénormalisation API : les données sont normalisées (volumes, editions, series, boxes, box_editions séparés)
- Indicateur de possession : croisement avec la collection de l'utilisateur
- Performance : optimisation pour Raspberry Pi

## Goals / Non-Goals

### Goals
- Afficher les dernières sorties (volumes et coffrets) dans une grille scrollable
- Indiquer visuellement si un volume est déjà possédé par l'utilisateur
- Badge "Dernier tome" pour les fins de série
- Navigation vers la fiche détail au clic
- Pull-to-refresh pour rafraîchir les données

### Non-Goals
- Filtre par éditeur (V1.1)
- Pagination infinie (V2)
- Recherche dans les news (hors scope)

## Decisions

### 1. Architecture des composants

```
NewsPage.qml
    ├── GridView (délégué: NewsCard.qml)
    │   ├── VolumeCard (volumes classiques)
    │   └── BoxCard (coffrets)
    └── PullToRefresh

NewsController (Python)
    └── NewsService
        ├── MangacollecApi.get_news()
        ├── MangacollecApi.get_collection() [existant]
        └── CacheManager (optionnel pour V1)
```

**Rationale** : Suivre le pattern établi par CollectionPage pour la cohérence.

### 2. Modèle de données unifié

Créer un `NewsItemDTO` qui unifie volumes et coffrets :

```python
@dataclass
class NewsItemDTO:
    item_id: str           # volume_id ou box_id
    item_type: str         # "volume" ou "box"
    title: str             # Titre de la série/édition coffret
    number: int            # Numéro du tome/coffret
    image_url: str
    release_date: date
    is_owned: bool         # Possédé par l'utilisateur
    is_last_volume: bool   # Dernier tome de la série
    series_title: str      # Pour les volumes
    edition_title: str     # Pour les coffrets
```

**Rationale** : Un seul modèle pour alimenter le GridView, avec distinction par `item_type`.

### 3. Stratégie de cache

- **TTL** : 1 heure (les news changent peu fréquemment)
- **Stockage** : En mémoire uniquement pour V1 (pas de persistance SQLite)
- **Invalidation** : Au pull-to-refresh ou après 1h

**Rationale** : Simplicité pour V1, le cache SQLite sera ajouté si nécessaire.

### 4. Détection "Dernier tome"

Un volume est considéré comme "dernier tome" si :
```python
is_last_volume = (volume.number == edition.last_volume_number) and edition.last_volume_number is not None
```

**Rationale** : Utiliser `last_volume_number` de l'édition quand disponible.

### 5. Détection "Déjà possédé"

Croiser avec la collection utilisateur chargée au démarrage :
```python
is_owned = volume_id in user_possession_ids
```

**Rationale** : La collection est déjà chargée par CollectionService, réutiliser ces données.

## Risks / Trade-offs

| Risque | Mitigation |
|--------|-----------|
| Données volumineuses | Limiter à 100 items côté API (pagination future) |
| Latence réseau | Afficher skeleton/loading pendant le chargement |
| Cache mémoire overflow | Limiter la durée de vie à 1h, pas de persistance |

## Migration Plan

Aucune migration nécessaire - nouvelle fonctionnalité.

## Open Questions

1. ~~Faut-il un onglet séparé pour volumes vs coffrets ?~~ → Non, grille unifiée pour V1
2. ~~Ordre de tri ?~~ → Par date de sortie décroissante (plus récent en premier)
