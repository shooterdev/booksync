# Capability: Catalog Extended (Search + Author/Publisher Details)

## Purpose

La fonctionnalité Catalog Extended enrichit la navigation en permettant la recherche par auteurs et éditeurs, et en offrant des fiches détaillées pour chaque créateur et éditeur. Elle complète la recherche par titre et facilite la découverte de mangas en explorant les œuvres d'un auteur, le catalogue d'un éditeur, et les sorties à venir de chaque éditeur.

---

## ADDED Requirements

### Requirement: Search by Author

The system SHALL allow searching for authors by name via Mangacollec API.

#### Scenario: Recherche d'auteur réussie

- **GIVEN** un utilisateur sur l'onglet "Auteurs" de la recherche
- **WHEN** l'utilisateur tape un nom d'auteur (ex: "Oda")
- **THEN** le système affiche une liste d'auteurs correspondants
- **AND** chaque résultat affiche: nom, nombre d'œuvres, extrait de biographie
- **AND** les résultats sont triés par pertinence (correspondance de nom)

#### Scenario: Aucun résultat auteur

- **GIVEN** un utilisateur ayant recherché un auteur
- **WHEN** aucun auteur ne correspond à la recherche
- **THEN** le système affiche "Aucun auteur trouvé pour 'XYZ'"
- **AND** une suggestion propose de vérifier l'orthographe

#### Scenario: Recherche partielle

- **GIVEN** un utilisateur tapant "Od" au lieu de "Oda"
- **WHEN** la recherche s'exécute
- **THEN** le système affiche les auteurs dont le nom commence par "Od"
- **AND** "Oda" apparaît dans les résultats

---

### Requirement: Search by Publisher

The system SHALL allow searching for publishers by name via Mangacollec API.

#### Scenario: Recherche d'éditeur réussie

- **GIVEN** un utilisateur sur l'onglet "Éditeurs" de la recherche
- **WHEN** l'utilisateur tape un nom d'éditeur (ex: "Kazé")
- **THEN** le système affiche une liste d'éditeurs correspondants
- **AND** chaque résultat affiche: nom, statut (Actif/Fermé), nombre de séries
- **AND** un badge indique si l'éditeur est fermé

#### Scenario: Éditeur fermé

- **GIVEN** un résultat d'éditeur fermé
- **WHEN** le système l'affiche
- **THEN** un indicateur "Fermé" est visible
- **AND** la fiche éditeur peut toujours être consultée pour l'historique

#### Scenario: Aucun résultat éditeur

- **GIVEN** un utilisateur ayant recherché un éditeur
- **WHEN** aucun éditeur ne correspond
- **THEN** le système affiche "Aucun éditeur trouvé pour 'XYZ'"

---

### Requirement: Search Tabs

The system SHALL display 3 tabs in the search page: Titles, Authors, Publishers.

#### Scenario: Basculement onglet Titres

- **GIVEN** un utilisateur sur l'onglet "Auteurs" de la recherche
- **WHEN** l'utilisateur clique sur "Titres"
- **THEN** la vue affiche les résultats de séries/volumes
- **AND** le champ de recherche garde la même requête
- **AND** les résultats correspondent au texte saisi

#### Scenario: Basculement onglet Auteurs

- **GIVEN** un utilisateur sur l'onglet "Titres"
- **WHEN** l'utilisateur clique sur "Auteurs"
- **THEN** la vue change et affiche les résultats d'auteurs pour le même texte
- **AND** la recherche est relancée si le texte a changé

#### Scenario: Basculement onglet Éditeurs

- **GIVEN** un utilisateur ayant saisi une recherche
- **WHEN** l'utilisateur clique sur "Éditeurs"
- **THEN** les résultats d'éditeurs correspondants s'affichent
- **AND** le focus reste sur le champ de recherche pour modification

#### Scenario: Recherche commune pour tous les onglets

- **GIVEN** un utilisateur changement d'onglet sans fermer la recherche
- **WHEN** le champ de recherche affiche "Shonen"
- **THEN** chaque onglet conserve le même terme
- **AND** les résultats sont filtrés selon l'onglet sélectionné

---

### Requirement: Author Detail Page

The system SHALL display an author's profile with name, biography, and grid of all their works with roles.

#### Scenario: Affichage fiche auteur complète

- **GIVEN** un utilisateur accédant à la fiche d'un auteur
- **WHEN** la page se charge
- **THEN** le système affiche: photo/avatar, nom complet, biographie, nombre total d'œuvres
- **AND** une grille des séries affiche toutes les œuvres de cet auteur

#### Scenario: Affichage rôles auteur

- **GIVEN** une fiche auteur avec plusieurs œuvres
- **WHEN** chaque série s'affiche
- **THEN** le rôle de l'auteur est indiqué (ex: "Scénariste", "Dessinateur", "Auteur complet")
- **AND** le rôle est visible et distinct dans la carte

#### Scenario: Navigation depuis fiche auteur

- **GIVEN** un utilisateur sur la fiche d'un auteur
- **WHEN** l'utilisateur clique sur une série
- **THEN** la navigation vers `SerieDetailPage` est effectuée
- **AND** la fiche auteur reste accessible via le bouton retour

#### Scenario: Chargement fiche auteur

- **GIVEN** un utilisateur accédant à la fiche auteur
- **WHEN** les données se chargent
- **THEN** un spinner est affiché
- **AND** l'interface reste réactive

---

### Requirement: Publisher Detail Page

The system SHALL display a publisher's profile with recent releases, upcoming releases, and complete alphabetical series catalog.

#### Scenario: Affichage fiche éditeur complète

- **GIVEN** un utilisateur accédant à la fiche d'un éditeur actif
- **WHEN** la page se charge
- **THEN** le système affiche: logo/nom, statut "Actif", nombre total de séries
- **AND** trois sections sont visibles: "Dernières sorties", "Prochaines sorties", "Catalogue"

#### Scenario: Dernières sorties éditeur

- **GIVEN** une fiche éditeur
- **WHEN** l'utilisateur voit la section "Dernières sorties"
- **THEN** 6-8 derniers volumes publiés par cet éditeur sont affichés en grille
- **AND** chaque carte affiche: couverture, titre, numéro, date sortie

#### Scenario: Prochaines sorties éditeur

- **GIVEN** une fiche éditeur
- **WHEN** l'utilisateur consulte la section "Prochaines sorties"
- **THEN** 6-8 prochains volumes à paraître sont affichés
- **AND** la date de sortie est visible pour chaque volume

#### Scenario: Catalogue alphabétique complet

- **GIVEN** une fiche éditeur avec des dizaines de séries
- **WHEN** l'utilisateur scroll vers la section "Catalogue"
- **THEN** toutes les séries de l'éditeur s'affichent dans une liste alphabétique avec défilement vertical
- **AND** les séries sont groupées par première lettre (optionnel: index A-Z sur le côté)

#### Scenario: Navigation depuis catalogue éditeur

- **GIVEN** un utilisateur sur la fiche d'un éditeur
- **WHEN** l'utilisateur clique sur une série dans le catalogue
- **THEN** la navigation vers `SerieDetailPage` est effectuée
- **AND** la fiche éditeur reste accessible

#### Scenario: Éditeur fermé

- **GIVEN** une fiche d'éditeur fermé
- **WHEN** la page s'affiche
- **THEN** un badge "Fermé" est visible près du nom
- **AND** les sections "Dernières sorties" et "Prochaines sorties" peuvent être vides
- **AND** le catalogue reste consultable pour l'historique

#### Scenario: Erreur chargement fiche éditeur

- **GIVEN** un utilisateur accédant à la fiche éditeur
- **WHEN** une erreur réseau survient
- **THEN** un message d'erreur s'affiche
- **AND** une action "Réessayer" est proposée
