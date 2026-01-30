# news-page Specification

## Purpose
TBD - created by archiving change add-news-page. Update Purpose after archive.
## Requirements
### Requirement: Affichage des dernières sorties

Le système SHALL afficher une grille des dernières sorties de volumes et coffrets depuis l'API Mangacollec.

#### Scenario: Chargement initial réussi
- **GIVEN** l'utilisateur est authentifié
- **WHEN** l'utilisateur accède à la page News
- **THEN** le système affiche un indicateur de chargement
- **AND** le système récupère les données depuis `GET /v2/volumes/news`
- **AND** le système affiche une grille de cartes (volumes et coffrets)

#### Scenario: Grille vide
- **GIVEN** l'API retourne une liste vide
- **WHEN** les données sont chargées
- **THEN** le système affiche un message "Aucune nouveauté disponible"

#### Scenario: Erreur de chargement
- **GIVEN** l'API retourne une erreur
- **WHEN** le chargement échoue
- **THEN** le système affiche un message d'erreur
- **AND** le système propose un bouton "Réessayer"

---

### Requirement: Carte de nouveauté (NewsCard)

Chaque item dans la grille SHALL afficher les informations essentielles du volume ou coffret.

#### Scenario: Affichage d'un volume
- **GIVEN** un volume dans la liste des nouveautés
- **WHEN** la carte est affichée
- **THEN** la carte affiche l'image de couverture
- **AND** la carte affiche le titre de la série
- **AND** la carte affiche le numéro du tome (ex: "Tome 5")
- **AND** la carte affiche la date de sortie

#### Scenario: Affichage d'un coffret
- **GIVEN** un coffret dans la liste des nouveautés
- **WHEN** la carte est affichée
- **THEN** la carte affiche l'image de couverture
- **AND** la carte affiche le titre de l'édition coffret
- **AND** la carte affiche le titre du coffret (ex: "Edition Collector Tome 16")

---

### Requirement: Indicateur de possession

Le système SHALL indiquer visuellement si l'utilisateur possède déjà un volume.

#### Scenario: Volume possédé
- **GIVEN** un volume présent dans la collection de l'utilisateur
- **WHEN** la carte est affichée
- **THEN** la carte affiche un indicateur visuel "Possédé" (icône check ou badge)

#### Scenario: Volume non possédé
- **GIVEN** un volume absent de la collection de l'utilisateur
- **WHEN** la carte est affichée
- **THEN** la carte n'affiche pas d'indicateur de possession

---

### Requirement: Badge dernier tome

Le système SHALL afficher un badge "Dernier tome" pour les volumes qui terminent une série.

#### Scenario: Volume est le dernier tome
- **GIVEN** un volume dont le numéro correspond au `last_volume_number` de l'édition
- **AND** l'édition a un `last_volume_number` défini (non null)
- **WHEN** la carte est affichée
- **THEN** la carte affiche un badge "Dernier tome"

#### Scenario: Volume n'est pas le dernier tome
- **GIVEN** un volume dont le numéro ne correspond pas au `last_volume_number`
- **OR** l'édition n'a pas de `last_volume_number` défini
- **WHEN** la carte est affichée
- **THEN** la carte n'affiche pas de badge "Dernier tome"

---

### Requirement: Rafraîchissement des données

Le système SHALL permettre à l'utilisateur de rafraîchir manuellement les données.

#### Scenario: Pull-to-refresh
- **GIVEN** la page News est affichée
- **WHEN** l'utilisateur effectue un geste de pull-to-refresh
- **THEN** le système affiche un indicateur de rafraîchissement
- **AND** le système recharge les données depuis l'API
- **AND** le système met à jour la grille avec les nouvelles données

#### Scenario: Cache expiré
- **GIVEN** les données en cache ont plus d'1 heure
- **WHEN** l'utilisateur accède à la page News
- **THEN** le système recharge automatiquement les données depuis l'API

---

### Requirement: Navigation vers le détail

Le système SHALL permettre la navigation vers la fiche détail d'un item.

#### Scenario: Clic sur un volume
- **GIVEN** un volume dans la grille
- **WHEN** l'utilisateur clique sur la carte
- **THEN** le système navigue vers la page VolumeDetailPage
- **AND** le système passe l'identifiant du volume

#### Scenario: Clic sur un coffret
- **GIVEN** un coffret dans la grille
- **WHEN** l'utilisateur clique sur la carte
- **THEN** le système navigue vers la page BoxDetailPage (si implémentée)
- **OR** le système affiche un message "Détail coffret non disponible" (V1)

---

### Requirement: Accès depuis la navigation

La page News SHALL être accessible depuis la navigation latérale.

#### Scenario: Navigation depuis SideBar
- **GIVEN** l'utilisateur est sur n'importe quelle page
- **WHEN** l'utilisateur clique sur "Accueil" dans la SideBar
- **THEN** le système affiche la page News
- **AND** le système charge les données si nécessaire

#### Scenario: Page par défaut après connexion
- **GIVEN** l'utilisateur vient de se connecter
- **WHEN** l'application affiche le MainLayout
- **THEN** la page News est affichée par défaut

