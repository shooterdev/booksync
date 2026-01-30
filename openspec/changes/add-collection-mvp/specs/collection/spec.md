# Capability: Collection

## Purpose

La fonctionnalité Collection permet à l'utilisateur de visualiser et gérer sa collection de mangas (volumes possédés). 
C'est le cœur de l'application BookSync, offrant une vue complète de tous les tomes avec leur progression par série.

---

## ADDED Requirements

### Requirement: Collection Display

The system SHALL display the complete list of volumes owned by the user as a responsive grid.

#### Scenario: Affichage initial de la collection

- **GIVEN** un utilisateur authentifié avec des volumes dans sa collection
- **WHEN** l'utilisateur accède à la page Collection
- **THEN** le système affiche une grille de cartes représentant chaque volume possédé
- **AND** chaque carte affiche: couverture, titre du volume, numéro du tome, titre de la série

#### Scenario: Collection vide

- **GIVEN** un utilisateur authentifié sans volumes dans sa collection
- **WHEN** l'utilisateur accède à la page Collection
- **THEN** le système affiche un message "Votre collection est vide"
- **AND** le système propose une action pour découvrir des mangas

#### Scenario: Chargement de la collection

- **GIVEN** un utilisateur qui accède à la page Collection
- **WHEN** les données sont en cours de chargement
- **THEN** le système affiche un indicateur de chargement (spinner)
- **AND** l'interface reste réactive

---

### Requirement: Series Progress

The system SHALL display the possession progress for each series in the collection.

#### Scenario: Affichage progression série

- **GIVEN** un utilisateur possédant plusieurs tomes d'une même série
- **WHEN** le système affiche un volume de cette série
- **THEN** la carte du volume indique la progression (ex: "3/10 tomes possédés")
- **AND** une barre de progression visuelle accompagne le texte

#### Scenario: Série complète

- **GIVEN** un utilisateur possédant tous les tomes d'une série terminée
- **WHEN** le système affiche un volume de cette série
- **THEN** la carte affiche un badge "Complète" ou indicateur visuel distinct

---

### Requirement: Collection Sorting

The system SHALL allow sorting the collection by different criteria.

#### Scenario: Tri par date d'ajout (défaut)

- **GIVEN** un utilisateur sur la page Collection
- **WHEN** aucun tri n'est sélectionné
- **THEN** les volumes sont triés par date d'ajout décroissante (plus récents en premier)

#### Scenario: Tri alphabétique par série

- **GIVEN** un utilisateur sur la page Collection
- **WHEN** l'utilisateur sélectionne le tri "Alphabétique"
- **THEN** les volumes sont regroupés par série et triés alphabétiquement par titre de série
- **AND** au sein d'une série, les tomes sont triés par numéro croissant

#### Scenario: Persistance du tri

- **GIVEN** un utilisateur ayant sélectionné un mode de tri
- **WHEN** l'utilisateur quitte et revient sur la page Collection
- **THEN** le mode de tri précédemment sélectionné est conservé

---

### Requirement: Volume Removal

The system SHALL allow removing a volume from the collection with confirmation.

#### Scenario: Retrait via action contextuelle

- **GIVEN** un utilisateur sur la page Collection
- **WHEN** l'utilisateur effectue un appui long ou swipe sur une carte volume
- **THEN** le système affiche une option "Retirer de la collection"
- **AND** une confirmation est demandée avant suppression

#### Scenario: Confirmation de retrait

- **GIVEN** un utilisateur ayant choisi de retirer un volume
- **WHEN** l'utilisateur confirme l'action
- **THEN** le volume est retiré de la collection via l'API
- **AND** la carte disparaît de la grille avec animation
- **AND** un toast confirme "Volume retiré de la collection"

#### Scenario: Annulation de retrait

- **GIVEN** un utilisateur ayant choisi de retirer un volume
- **WHEN** l'utilisateur annule l'action
- **THEN** le volume reste dans la collection
- **AND** l'interface revient à l'état normal

#### Scenario: Erreur lors du retrait

- **GIVEN** un utilisateur tentant de retirer un volume
- **WHEN** une erreur réseau survient
- **THEN** le système affiche un message d'erreur
- **AND** le volume reste affiché dans la collection
- **AND** une option "Réessayer" est proposée

---

### Requirement: Offline Reading Mode

The system MUST allow browsing the collection in offline mode (read-only) using local cache.

#### Scenario: Consultation offline

- **GIVEN** un utilisateur sans connexion réseau
- **AND** des données de collection en cache local
- **WHEN** l'utilisateur accède à la page Collection
- **THEN** le système affiche les données du cache
- **AND** un indicateur "Mode hors-ligne" est visible

#### Scenario: Action bloquée offline

- **GIVEN** un utilisateur sans connexion réseau
- **WHEN** l'utilisateur tente de retirer un volume
- **THEN** le système affiche "Action impossible hors connexion"
- **AND** l'action est bloquée

#### Scenario: Reconnexion

- **GIVEN** un utilisateur précédemment hors-ligne
- **WHEN** la connexion réseau est rétablie
- **THEN** le système actualise automatiquement les données
- **AND** l'indicateur "Mode hors-ligne" disparaît

---

### Requirement: Collection Refresh

The system SHALL allow manual and automatic refresh of the collection from the API.

#### Scenario: Actualisation manuelle

- **GIVEN** un utilisateur sur la page Collection
- **WHEN** l'utilisateur effectue un pull-to-refresh ou clique sur actualiser
- **THEN** le système récupère les données fraîches depuis l'API
- **AND** la grille est mise à jour avec les nouvelles données
- **AND** le cache local est actualisé

#### Scenario: Actualisation automatique

- **GIVEN** un cache de collection datant de plus de 5 minutes
- **WHEN** l'utilisateur accède à la page Collection
- **THEN** le système actualise automatiquement les données en arrière-plan
- **AND** l'interface affiche d'abord les données du cache puis se met à jour
