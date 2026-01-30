# Design: Catalog Pages Navigation & Architecture

## Context
L'application doit afficher des fiches détaillées de volumes, séries et éditions avec une navigation fluide entre elles. L'utilisateur doit pouvoir consulter une série, explorer ses éditions, et consulter les détails de chaque tome. Les données proviennent d'une combinaison de cache local (TinyDB) et d'appels API Mangacollec pour les détails manquants.

Le Raspberry Pi 4 avec écran 1280x720 dispose de ressources limitées (RAM, CPU). L'architecture doit optimiser les chargements et éviter les re-renderings excessifs.

## Goals
- Fournir une expérience de navigation fluide entre fiches (Série → Édition → Volume)
- Charger les données de manière efficace (cache d'abord, API secondaire)
- Maintenir l'état de navigation pour permettre le retour sans perte de contexte
- Optimiser la performance sur Raspberry Pi avec pagination/lazy loading si nécessaire

Non-Goals:
- Télécharger et stocker toutes les images de couvertures (utiliser le streaming)
- Créer une vue 3D ou des animations complexes
- Implémenter un système de recommandations/suggestions

## Decisions

### Decision 1: Navigation via StackView
**What**: Utiliser QML StackView pour gérer la pile des pages de détail (Volume, Série, Édition).

**Why**:
- StackView est le pattern QML standard pour les navigations avec retour
- Gestion automatique du bouton retour
- Préservation de l'état des pages lors du pop/push

**Alternatives considered**:
- Loader dynamique + state machine : plus flexible mais plus complexe à gérer
- Page loader unique : plus simple mais perd l'historique de navigation

### Decision 2: Un seul CatalogService pour les 3 types de fiches
**What**: Créer une classe CatalogService unique qui expose get_volume(), get_series(), get_edition() plutôt que des services séparés.

**Why**:
- Réduit la duplication de code pour les appels API/cache
- Centralise la logique de gestion du cache TinyDB
- Facilite la maintenance et les tests

**Alternatives considered**:
- Services séparés (VolumeService, SeriesService, EditionService) : meilleure séparation des préoccupations mais plus verbeux
- Service générique avec patterns : trop abstrait pour cette MVP

### Decision 3: Lazy Loading des données non cachées
**What**: Charger d'abord les données du cache TinyDB, puis appeler l'API pour les détails manquants (résumé, statistiques, etc) en arrière-plan.

**Why**:
- Affichage rapide des informations de base (titre, type, éditeur)
- API calls asynchrones ne bloquent pas l'UI
- Optimise la bande passante pour les données secondaires

**Alternatives considered**:
- Charger tout avant d'afficher : trop lent pour RPi
- Charger seulement du cache : incomplet, ne montre pas les statistiques

### Decision 4: Grille des tomes avec badges de possession
**What**: Dans EditionDetailPage, afficher une grille (3-4 colonnes max) avec chaque tome numéroté et un badge vert "Possédé" pour les tomes de l'utilisateur.

**Why**:
- Vue d'ensemble visuelle des tomes
- Les badges sont cliquables pour consulter le détail du tome
- Adapté à l'écran tactile 1280x720

**Alternatives considered**:
- Liste verticale : moins visuelle, plus de scroll
- Pagination : complexifie la navigation

## Risks & Mitigations

### Risk 1: Performance sur RPi si trop de données chargées
**Mitigation**: Implémenter une pagination pour les éditions (afficher 10 à la fois), et lazy loading pour les images de couvertures. Utiliser des thumbnails plutôt que images full-res.

### Risk 2: État de navigation perdu lors du retour
**Mitigation**: Utiliser le StackView qui préserve l'état des pages automatiquement. Pour les listes (liste d'éditions dans Série), sauvegarder la position du scroll.

### Risk 3: Appels API excessifs lors de la navigation rapide
**Mitigation**: Implémenter un cache au niveau du ViewModel et throttle les appels API (ignorer les appels si une requête est en cours).

## Migration Plan

1. **Phase 1**: Implémenter CatalogService et les 3 ViewModels sans UI
2. **Phase 2**: Créer VolumeDetailPage.qml avec actions (ajouter/retirer)
3. **Phase 3**: Créer SeriesDetailPage.qml et navigation vers éditions
4. **Phase 4**: Créer EditionDetailPage.qml avec grille des tomes
5. **Phase 5**: Intégrer StackView dans MainLayout et tester la navigation
6. **Phase 6**: Optimisation performance et validation sur RPi

## Open Questions

- L'API Mangacollec retourne-t-elle les statistiques communautaires (possessions_count) ? Si non, où les récupérer ?
- Doit-on stocker en cache les fiches visitées pour une consultation hors-ligne, ou l'API est-elle toujours disponible ?
- Quel est le nombre maximum de tomes par édition à gérer (pagination) ?
