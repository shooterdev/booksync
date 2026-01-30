# Change: Ajout du Planning des sorties (V1)

## Why

Les utilisateurs doivent pouvoir consulter les sorties à venir pour ne pas rater les nouveaux tomes de leurs séries suivies et découvrir les prochaines parutions du catalogue. Le Planning est une fonctionnalité clé pour maintenir l'engagement et aider à la décision d'achat.

## What Changes

### Nouveaux fichiers/composants

**Services:**
- `PlanningService` — logique métier pour récupérer et filtrer les releases
- `ReleaseCache` — cache SQLite avec TTL 1h des releases

**Présentation:**
- `PlanningViewModel` — QObject pour gérer l'état du planning
- `ReleaseListModel` — QAbstractListModel pour affichage des releases
- Composants QML: `PlanningContainer.qml`, `PlanningPersonalise.qml`, `PlanningAll.qml`, `ReleaseCard.qml`

**Intégration:**
- Navigation SideBar vers Planning
- Fil d'Ariane et intégration avec MainLayout

## Impact

- **Affected specs**: Nouvelle capability `planning` (aucune spec existante)
- **Affected code**:
  - `booksync_app_qt/src/services/` — PlanningService
  - `booksync_app_qt/src/viewmodels/` — PlanningViewModel
  - `booksync_app_qt/src/models/` — ReleaseListModel
  - `booksync_app_qt/qml/pages/` — Planning pages
  - `booksync_app_qt/qml/components/` — ReleaseCard, SubNavBar
  - `SideBar.qml` — ajout entrée Planning
- **Breaking changes**: Aucun

## Scope

Cette proposition couvre la **vue Planning V1**:
- Affichage des sorties à venir groupées par semaine
- Onglet Personnalisé : sorties des éditions suivies
- Onglet Tout : sorties du catalogue global avec filtre éditeur
- Cartes releases avec couverture, titre, numéro, date, éditeur, indicateur possession
- Actualisation manuelle (pull-to-refresh) et automatique (TTL 1h)

**Hors scope** (futures versions):
- Notifications pour sorties imminentes
- Pré-commande de volumes
- Historique des sorties passées
