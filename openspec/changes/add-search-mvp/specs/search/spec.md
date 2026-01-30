# Specification: Search MVP

## ADDED Requirements

### Requirement: Search by Series Title
Le système SHALL permettre la recherche de séries par titre via l'API Mangacollec (endpoint GET /v2/search).

#### Scenario: Recherche réussie avec résultats
- **WHEN** l'utilisateur saisit "One Piece" dans le champ de recherche et valide
- **THEN** l'API Mangacollec est appelée avec le paramètre de titre
- **AND** les résultats de recherche sont affichés dans la liste

#### Scenario: Recherche avec zéro résultat
- **WHEN** l'utilisateur saisit un titre inexistant comme "XxxYyyzz123NoResult"
- **THEN** l'API retourne une liste vide
- **AND** un message "Aucun résultat pour votre recherche" est affiché

#### Scenario: Recherche vide
- **WHEN** l'utilisateur n'a pas saisi de texte ou efface tout
- **THEN** aucun appel API n'est effectué
- **AND** un message d'invitation "Entrez un titre pour rechercher" est affiché

### Requirement: Search Results Display
Le système SHALL afficher les résultats de recherche sous forme de liste scrollable avec les informations suivantes pour chaque série : couverture (image de la première édition ou placeholder), titre de la série, type (manga/manhwa/manhwa-scan/one-shot/etc), et nombre d'éditions.

#### Scenario: Affichage des résultats trouvés
- **WHEN** la recherche retourne 5 séries
- **THEN** chaque série est affichée sous forme de carte avec couverture en petit format
- **AND** le titre, le type et le nombre d'éditions sont visibles
- **AND** la liste est scrollable verticalement

#### Scenario: Affichage avec image manquante
- **WHEN** une série n'a pas de couverture disponible
- **THEN** un placeholder gris avec l'icône manga est affiché
- **AND** le titre et les infos sont toujours visibles

#### Scenario: Affichage sur écran tactile
- **WHEN** l'interface s'affiche sur écran 1280x720
- **THEN** la liste affiche 3-4 éléments par écran sans overflow horizontal
- **AND** les éléments sont tactiles et cliquables

### Requirement: Search Debounce
Le système SHALL appliquer un debounce de 300ms sur la saisie utilisateur pour éviter les appels API excessifs lors de la frappe rapide.

#### Scenario: Frappe rapide sans debounce
- **WHEN** l'utilisateur frappe rapidement "O", "n", "e", "P", "i", "e", "c", "e"
- **THEN** un seul appel API est effectué pour "OnePiece" (après 300ms de pause)
- **AND** pas d'appels API pour chaque lettre intermédiaire

#### Scenario: Modifications successives avec pause
- **WHEN** l'utilisateur saisit "Naruto", fait une pause de 300ms, puis ajoute " Shippuden"
- **THEN** deux appels API sont effectués (un pour "Naruto", un pour "Naruto Shippuden")

#### Scenario: Annulation rapide
- **WHEN** l'utilisateur saisit "Tokyo", puis efface rapidement tout
- **THEN** un seul appel API pour "Tokyo" est effectué
- **AND** aucun appel API pour la recherche vide n'est effectué

### Requirement: Search Result Navigation
Le système SHALL permettre la navigation vers la fiche détaillée d'une série depuis un résultat de recherche.

#### Scenario: Navigation vers fiche série
- **WHEN** l'utilisateur clique sur un résultat de recherche "Attack on Titan"
- **THEN** la page de détail de la série s'ouvre
- **AND** toutes les informations de la série (type, genres, éditions) sont chargées
- **AND** un bouton "Retour" permet de revenir à la recherche

#### Scenario: Maintien de l'état de recherche
- **WHEN** l'utilisateur retourne de la fiche série vers la recherche
- **THEN** le texte de recherche et les résultats précédents sont préservés
- **AND** l'utilisateur peut continuer sa navigation sans ressaisir la requête

#### Scenario: Acception des entrées clavier
- **WHEN** l'utilisateur appuie sur Entrée après avoir saisi un titre
- **THEN** la recherche est lancée immédiatement
- **AND** la liste de résultats s'affiche

## MODIFIED Requirements
