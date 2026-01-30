# Capability: collection-sections

## Purpose

Fournir une navigation structurée entre les différentes sections de la Collection (Pile à lire, Collection, Compléter, Envies, Statistiques, Historiques) avec une barre de sous-navigation contextuelle.

---

## ADDED Requirements

### Requirement: Composant SubNavBar

Le système SHALL afficher une barre de sous-navigation avec des onglets horizontaux cliquables permettant de naviguer entre les sections d'un conteneur.

#### Scenario: Navigation entre onglets
- **GIVEN** l'utilisateur est sur CollectionContainer avec SubNavBar
- **WHEN** l'utilisateur clique sur l'onglet "Envies"
- **THEN** la page EnviesPage s'affiche
- **AND** l'onglet "Envies" est visuellement marqué comme actif

#### Scenario: Onglets désactivés
- **GIVEN** l'utilisateur est sur CollectionContainer
- **WHEN** l'utilisateur survole un onglet désactivé (V2+)
- **THEN** un tooltip "Bientôt disponible" s'affiche
- **AND** le clic n'a aucun effet

---

### Requirement: Navigation CollectionContainer

Le conteneur Collection SHALL gérer la navigation entre ses sous-sections via SubNavBar.

#### Scenario: Chargement initial
- **GIVEN** l'utilisateur navigue vers Collection depuis la sidebar
- **WHEN** CollectionContainer se charge
- **THEN** la section "Collection" (index 1) est affichée par défaut
- **AND** SubNavBar affiche tous les onglets avec "Collection" actif

#### Scenario: Préservation de l'état
- **GIVEN** l'utilisateur est sur la section "Envies"
- **WHEN** l'utilisateur navigue vers une autre page principale puis revient
- **THEN** la section "Envies" est restaurée

---

### Requirement: Page Compléter (tomes manquants)

La page Compléter SHALL afficher les volumes manquants dans les séries incomplètes de l'utilisateur.

#### Scenario: Affichage des tomes manquants
- **GIVEN** l'utilisateur possède les tomes 1, 2, 5 d'une série de 10 tomes
- **WHEN** l'utilisateur affiche la page Compléter
- **THEN** les tomes 3, 4, 6, 7, 8, 9, 10 sont listés comme manquants
- **AND** ils sont groupés par série

#### Scenario: Collection complète
- **GIVEN** l'utilisateur possède tous les tomes de toutes ses séries
- **WHEN** l'utilisateur affiche la page Compléter
- **THEN** un message "Votre collection est complète !" s'affiche

#### Scenario: Ajouter un tome manquant
- **GIVEN** l'utilisateur voit un tome manquant
- **WHEN** l'utilisateur clique sur "Ajouter"
- **THEN** le tome est ajouté à la collection
- **AND** il disparaît de la liste des manquants

---

### Requirement: Page Envies (Wishlist)

La page Envies SHALL afficher les volumes non possédés des éditions suivies par l'utilisateur.

#### Scenario: Affichage de la wishlist
- **GIVEN** l'utilisateur suit l'édition "One Piece - Glénat" et ne possède pas tous les tomes
- **WHEN** l'utilisateur affiche la page Envies
- **THEN** les tomes non possédés de cette édition sont listés
- **AND** ils affichent la couverture, le numéro et le titre de la série

#### Scenario: Wishlist vide
- **GIVEN** l'utilisateur ne suit aucune édition
- **WHEN** l'utilisateur affiche la page Envies
- **THEN** un message "Suivez des éditions pour voir vos envies" s'affiche

#### Scenario: Ajouter un volume souhaité
- **GIVEN** l'utilisateur voit un volume dans ses envies
- **WHEN** l'utilisateur clique sur "Ajouter à ma collection"
- **THEN** le volume est ajouté à la collection
- **AND** il disparaît de la liste des envies

---

### Requirement: Service Wishlist

Le WishlistService SHALL calculer dynamiquement les volumes souhaités à partir des éditions suivies.

#### Scenario: Calcul de la wishlist
- **GIVEN** l'utilisateur suit 3 éditions avec respectivement 10, 5, 20 tomes
- **AND** l'utilisateur possède 8, 5, 15 tomes de ces éditions
- **WHEN** WishlistService.get_wishlist() est appelé
- **THEN** la wishlist contient 7 volumes (2 + 0 + 5)

#### Scenario: Synchronisation avec l'API
- **GIVEN** le cache follow_editions est périmé
- **WHEN** WishlistService.get_wishlist() est appelé
- **THEN** les follow_editions sont synchronisées depuis l'API
- **AND** le cache est mis à jour

---

### Requirement: Détection des tomes manquants

Le CollectionService SHALL détecter les tomes manquants dans les séries partiellement possédées.

#### Scenario: Détection des trous
- **GIVEN** l'utilisateur possède les tomes 1, 3, 5 d'une édition de 10 tomes
- **WHEN** CollectionService.get_missing_volumes() est appelé
- **THEN** les tomes 2, 4, 6, 7, 8, 9, 10 sont retournés comme manquants

#### Scenario: Série inconnue du cache
- **GIVEN** l'utilisateur possède un tome d'une édition non cachée localement
- **WHEN** CollectionService.get_missing_volumes() est appelé
- **THEN** l'édition est récupérée depuis l'API
- **AND** les tomes manquants sont calculés

---

### Requirement: Badges compteurs SubNavBar

Les onglets de SubNavBar SHALL afficher des compteurs ou badges informatifs.

#### Scenario: Badge tomes manquants
- **GIVEN** l'utilisateur a 12 tomes manquants
- **WHEN** SubNavBar s'affiche
- **THEN** l'onglet "Compléter" affiche un badge "(12)"

#### Scenario: Badge envies
- **GIVEN** l'utilisateur a 5 volumes dans sa wishlist
- **WHEN** SubNavBar s'affiche
- **THEN** l'onglet "Envies" affiche un badge "(5)"
