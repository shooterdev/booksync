# Specification: Catalog Pages (Volume, Series, Edition)

## ADDED Requirements

### Requirement: Volume Detail Page
Le système SHALL afficher une fiche détaillée de volume avec : couverture plein écran (ou placeholder), titre du volume, numéro du tome, date de sortie, ISBN, résumé (field content), prix Amazon (ASIN), et statistiques communautaires (possessions_count).

#### Scenario: Ouverture fiche volume existant avec toutes les données
- **WHEN** l'utilisateur clique sur un volume depuis la grille d'édition ou une recherche
- **THEN** la page de détail du volume s'ouvre
- **AND** la couverture est affichée en grand format en haut
- **AND** le titre, le numéro, la date de sortie, l'ISBN et le résumé sont visibles
- **AND** le nombre de possessions communautaires est affiché

#### Scenario: Volume sans image de couverture
- **WHEN** un volume n'a pas d'image de couverture disponible
- **THEN** un placeholder gris avec icône manga est affiché
- **AND** le titre et le numéro du tome sont visibles sur le placeholder
- **AND** les autres informations restent accessibles

#### Scenario: Données partiellement manquantes
- **WHEN** un volume manque certaines données (résumé vide, ASIN absent, etc)
- **THEN** les champs manquants affichent "Non disponible" ou "—"
- **AND** la page reste fonctionnelle et navigable

#### Scenario: Affichage sur écran tactile 1280x720
- **WHEN** la page s'affiche sur écran 1280x720
- **THEN** la couverture est dimensionnée proportivement à l'écran
- **AND** le contenu est scrollable verticalement
- **AND** les éléments tactiles (boutons) ont une taille suffisante (min 44x44px)

### Requirement: Volume Actions
Le système SHALL permettre d'ajouter ou de retirer un volume de la collection depuis sa fiche détaillée, avec confirmation visuelle de l'action.

#### Scenario: Volume non possédé - ajout à la collection
- **WHEN** l'utilisateur consulte une fiche volume non possédée
- **THEN** un bouton "Ajouter à ma collection" est affiché
- **AND** au clic, l'API est appelée (POST /collections/volumes/{id})
- **AND** un loader s'affiche pendant la requête
- **AND** après succès, le bouton devient "Retirer de ma collection"
- **AND** un badge vert "Possédé" apparaît sur la couverture

#### Scenario: Volume possédé - retrait de la collection
- **WHEN** l'utilisateur consulte une fiche volume déjà possédée
- **THEN** un bouton "Retirer de ma collection" est affiché
- **AND** au clic, une confirmation s'affiche ("Êtes-vous sûr ?")
- **AND** après confirmation, l'API est appelée (DELETE /collections/volumes/{id})
- **AND** le bouton revient à "Ajouter à ma collection"
- **AND** le badge "Possédé" disparaît

#### Scenario: Erreur lors de l'action
- **WHEN** l'API retourne une erreur (timeout, 500, etc)
- **THEN** un message d'erreur s'affiche ("Erreur réseau, veuillez réessayer")
- **AND** le bouton conserve son état précédent
- **AND** l'utilisateur peut relancer l'action

### Requirement: Volume Navigation (Tome Précédent/Suivant)
Le système SHALL permettre la navigation fluide entre les tomes d'une même édition via boutons "Tome précédent" et "Tome suivant".

#### Scenario: Navigation entre tomes dans une édition
- **WHEN** l'utilisateur consulte le tome 3 d'une édition avec 10 tomes
- **THEN** une flèche "← Tome 2" est affichée à gauche
- **AND** une flèche "Tome 4 →" est affichée à droite
- **AND** au clic sur une flèche, la fiche du tome adjacent s'affiche
- **AND** la page scrolle automatiquement en haut

#### Scenario: Navigation premier tome
- **WHEN** l'utilisateur est sur le tome 1
- **THEN** la flèche "← Tome précédent" est désactivée ou cachée
- **AND** la flèche "Tome suivant →" est active et cliquable

#### Scenario: Navigation dernier tome
- **WHEN** l'utilisateur est sur le dernier tome
- **THEN** la flèche "← Tome précédent" est active et cliquable
- **AND** la flèche "Tome suivant →" est désactivée ou cachée

### Requirement: Series Detail Page
Le système SHALL afficher une fiche détaillée de série avec : titre, type (manga/manhwa/manhwa-scan/one-shot/etc), genres (kinds) sous forme de tags, auteurs avec rôles (illustrateur, scénariste, etc), et liste scrollable des éditions de cette série.

#### Scenario: Ouverture fiche série existante
- **WHEN** l'utilisateur clique sur une série (depuis recherche, collection, ou volume)
- **THEN** la page de détail de la série s'ouvre
- **AND** le titre et le type de la série sont affichés en haut
- **AND** les genres sont affichés sous forme de tags cliquables ou non
- **AND** les auteurs avec leurs rôles sont listés (ex: "Eiichiro Oda (Scénariste/Illustrateur)")

#### Scenario: Affichage des éditions
- **WHEN** la fiche série est ouverte
- **THEN** une liste des éditions de la série s'affiche
- **AND** chaque édition affiche : nom, éditeur, statut (en cours/terminée/arrêt commercial), nombre de tomes
- **AND** la liste est scrollable et peut contenir plusieurs éditions
- **AND** clic sur une édition → navigation vers EditionDetailPage

#### Scenario: Auteurs sans rôle défini
- **WHEN** un auteur n'a pas de rôle spécifié par l'API
- **THEN** seul le nom de l'auteur s'affiche
- **AND** la liste reste lisible et complète

#### Scenario: Série sans genres
- **WHEN** une série n'a pas de genres attachés
- **THEN** la section "Genres" affiche "Non spécifiés"
- **AND** le reste de la page fonctionne normalement

### Requirement: Edition Detail Page
Le système SHALL afficher une fiche détaillée d'édition avec : titre, éditeur, statut (en cours/terminée/arrêt commercial), nombre de volumes parus et à paraître, et une grille de tous les tomes avec indicateurs visuels de possession.

#### Scenario: Ouverture fiche édition avec détails complets
- **WHEN** l'utilisateur clique sur une édition
- **THEN** la page de détail de l'édition s'ouvre
- **AND** le titre, l'éditeur et le statut sont affichés
- **AND** des statistiques s'affichent ("12 tomes parus, 3 à paraître")
- **AND** une grille (3-4 colonnes max) de tous les tomes s'affiche

#### Scenario: Affichage des badges de possession
- **WHEN** la grille des tomes est affichée
- **THEN** les tomes possédés par l'utilisateur affichent un badge vert "Possédé"
- **AND** les tomes non possédés restent sans badge
- **AND** chaque tome affiche son numéro en gros caractères

#### Scenario: Navigation depuis grille vers détail tome
- **WHEN** l'utilisateur clique sur un tome dans la grille
- **THEN** la fiche détaillée de ce volume s'ouvre (VolumeDetailPage)
- **AND** le bouton retour ramène vers la fiche édition

#### Scenario: Édition avec tomes non encore parus
- **WHEN** une édition contient des tomes avec date de sortie future
- **THEN** ces tomes affichent un badge gris "À paraître" ou une date
- **AND** les tomes déjà parus affichent normalement avec badge "Possédé" si applicable

#### Scenario: Affichage responsive sur écran 1280x720
- **WHEN** la grille s'affiche sur écran 1280x720
- **THEN** 3-4 colonnes max sont affichées
- **AND** les cellules de tome ont une taille tactile confortable (min 60x80px)
- **AND** le contenu est scrollable verticalement sans déborder horizontalement

### Requirement: Inter-page Navigation (Série ↔ Édition ↔ Volume)
Le système SHALL permettre la navigation transparente entre fiches de série, édition et volume, avec préservation de l'état et bouton retour fonctionnel.

#### Scenario: Navigation complète Série → Édition → Volume
- **WHEN** l'utilisateur ouvre une fiche série
- **THEN** il peut cliquer sur une édition pour ouvrir EditionDetailPage
- **AND** depuis EditionDetailPage, il peut cliquer sur un tome pour ouvrir VolumeDetailPage
- **AND** chaque page a un bouton "Retour" qui remonte la pile de navigation

#### Scenario: Retour préserve l'état de la page précédente
- **WHEN** l'utilisateur est dans VolumeDetailPage et clique "Retour"
- **THEN** la page EditionDetailPage s'affiche avec la grille au même position de scroll
- **AND** le clic sur une édition de EditionDetailPage retourne à la SeriesDetailPage
- **AND** la position de scroll dans la liste d'éditions est préservée

#### Scenario: Navigation directe vers détails
- **WHEN** l'utilisateur clique sur un volume depuis la SearchPage ou CollectionPage
- **THEN** la VolumeDetailPage s'ouvre directement
- **AND** le bouton retour ramène vers la page d'origine (SearchPage ou CollectionPage)

#### Scenario: Historique de navigation multi-niveaux
- **WHEN** l'utilisateur navigue Recherche → Série → Édition → Volume → Retour → Édition → Volume 2
- **THEN** la pile de navigation se gère correctement
- **AND** chaque "Retour" remonte d'un niveau dans la pile
- **AND** pas de boucles infinies ou pertes de contexte

## MODIFIED Requirements
