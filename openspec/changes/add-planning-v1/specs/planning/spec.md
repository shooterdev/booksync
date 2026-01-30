# Capability: Planning

## Purpose

La fonctionnalité Planning permet aux utilisateurs de consulter les sorties à venir de mangas. Elle offre deux vues complémentaires : une vue personnalisée affichant uniquement les sorties des éditions suivies, et une vue globale montrant toutes les sorties avec filtrage par éditeur. Cette fonctionnalité aide l'utilisateur à rester informé des nouvelles parutions et à planifier ses achats.

---

## ADDED Requirements

### Requirement: Personalized Planning Display

The system SHALL display upcoming releases only from user-followed editions grouped by week.

#### Scenario: Affichage sorties personnalisées

- **GIVEN** un utilisateur authentifié avec des éditions suivies
- **WHEN** l'utilisateur accède à l'onglet "Personnalisé" du Planning
- **THEN** le système affiche les sorties à venir uniquement de ces éditions
- **AND** les sorties sont groupées par semaine (ex: "Semaine du 02/02")
- **AND** chaque sortie affiche: couverture, titre série, numéro tome, date, éditeur

#### Scenario: Aucune sortie personnalisée

- **GIVEN** un utilisateur n'ayant aucune édition suivie
- **WHEN** l'utilisateur accède à l'onglet "Personnalisé"
- **THEN** le système affiche le message "Suivez des éditions pour voir vos sorties"
- **AND** une action propose de découvrir des éditeurs

#### Scenario: Aucune sortie suivie cette semaine

- **GIVEN** un utilisateur avec éditions suivies mais aucune sortie cette semaine
- **WHEN** l'utilisateur consulte le Planning personnalisé
- **THEN** le système affiche "Aucune sortie cette semaine" pour la semaine actuelle
- **AND** les semaines suivantes avec sorties sont affichées

---

### Requirement: Global Planning Display

The system SHALL display all upcoming releases in a global catalog view with publisher filtering.

#### Scenario: Affichage global des sorties

- **GIVEN** un utilisateur sur l'onglet "Tout" du Planning
- **WHEN** l'interface se charge
- **THEN** le système affiche toutes les sorties à venir du catalogue
- **AND** les sorties sont groupées par semaine
- **AND** un filtre "Tous les éditeurs" est appliqué par défaut

#### Scenario: Filtre par éditeur

- **GIVEN** un utilisateur sur l'onglet "Tout"
- **WHEN** l'utilisateur sélectionne un éditeur dans le filtre (ex: "Kazé")
- **THEN** le système affiche uniquement les sorties de cet éditeur
- **AND** les sorties sont réorganisées immédiatement
- **AND** le nombre total de sorties visibles change

#### Scenario: Aucune sortie pour le filtre

- **GIVEN** un utilisateur ayant sélectionné un filtre éditeur
- **WHEN** aucune sortie n'existe pour cet éditeur
- **THEN** le système affiche "Aucune sortie trouvée pour cet éditeur"

---

### Requirement: Release Card

The system SHALL display each release with cover, title, volume number, date, publisher, and ownership indicator.

#### Scenario: Affichage carte release complète

- **GIVEN** un utilisateur consultant le Planning
- **WHEN** une sortie s'affiche
- **THEN** la carte affiche: couverture (image), titre série, tome "#123", date "Semaine du XX/XX", éditeur
- **AND** un badge indique si l'utilisateur possède déjà ce volume

#### Scenario: Volume déjà possédé

- **GIVEN** une sortie d'un volume déjà dans la collection de l'utilisateur
- **WHEN** le Planning l'affiche
- **THEN** la carte affiche un badge "Possédé" ou indicateur visuel (couleur)
- **AND** le badge est visible et non intrusif

#### Scenario: Volume non possédé

- **GIVEN** une sortie d'un volume absent de la collection
- **WHEN** le Planning l'affiche
- **THEN** aucun badge de possession n'est affiché
- **AND** la carte invite implicitement à l'ajout (via couleur neutre)

#### Scenario: Image couverture manquante

- **GIVEN** une sortie sans image de couverture disponible
- **WHEN** la carte se charge
- **THEN** une image placeholder grise est affichée
- **AND** le titre et autres infos restent visibles

---

### Requirement: Planning Tab Navigation

The system SHALL allow navigation between "Personalized" and "Global" tabs via a SubNavBar.

#### Scenario: Basculement vers onglet Personnalisé

- **GIVEN** un utilisateur sur l'onglet "Tout"
- **WHEN** l'utilisateur clique sur "Personnalisé"
- **THEN** la vue change vers le Planning personnalisé
- **AND** une animation de transition est visible
- **AND** l'état précédent est restauré (scroll, sélection filtre)

#### Scenario: Basculement vers onglet Tout

- **GIVEN** un utilisateur sur l'onglet "Personnalisé"
- **WHEN** l'utilisateur clique sur "Tout"
- **THEN** la vue change vers le Planning global
- **AND** aucun filtre éditeur n'est appliqué (par défaut)
- **AND** le scroll est réinitialisé en haut

#### Scenario: Persitence de l'onglet actif

- **GIVEN** un utilisateur ayant sélectionné l'onglet "Tout"
- **WHEN** l'utilisateur quitte et revient au Planning
- **THEN** l'onglet "Tout" reste sélectionné
- **AND** le contenu est rechargé avec les données fraîches

---

### Requirement: Planning Data Refresh

The system SHALL refresh planning data from Mangacollec API (GET /v2/volumes/releases) with 1-hour cache TTL.

#### Scenario: Actualisation automatique après TTL

- **GIVEN** un utilisateur accédant au Planning
- **AND** le cache des releases date de plus de 1 heure
- **WHEN** l'interface se charge
- **THEN** le système affiche d'abord les données en cache
- **AND** il lance un refresh asynchrone en arrière-plan
- **AND** une fois les données fraîches reçues, la grille se met à jour automatiquement

#### Scenario: Cache valide (< 1 heure)

- **GIVEN** un utilisateur accédant au Planning
- **AND** le cache date de moins de 1 heure
- **WHEN** l'interface charge
- **THEN** aucun appel API n'est lancé
- **AND** les données du cache sont affichées immédiatement

#### Scenario: Pull-to-refresh manuel

- **GIVEN** un utilisateur sur le Planning
- **WHEN** l'utilisateur effectue un geste de pull-to-refresh (haut de l'écran)
- **THEN** le système affiche un spinner de chargement
- **AND** il lance un appel API pour récupérer les releases fraîches
- **AND** le cache est mis à jour
- **AND** le spinner disparaît à la fin

#### Scenario: Erreur réseau lors du refresh

- **GIVEN** un utilisateur tentant de rafraîchir le Planning
- **WHEN** une erreur réseau survient
- **THEN** le système affiche "Erreur de chargement"
- **AND** les données en cache restent visibles
- **AND** une action "Réessayer" est proposée

#### Scenario: Perte de connexion

- **GIVEN** un utilisateur sans connexion réseau
- **WHEN** le cache est expiré et un refresh est tenté
- **THEN** le système affiche les données du cache avec un label "Mode hors-ligne"
- **AND** aucune erreur n'est affichée si des données en cache existent
