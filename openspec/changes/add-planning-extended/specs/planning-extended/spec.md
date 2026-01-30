# Spec: Planning Nouveautés et Coffrets

## ADDED Requirements

### Requirement: New Series Planning

Le système SHALL afficher les prochaines sorties de premiers tomes (number=1), one-shots et guidebooks uniquement.

#### Scenario: Nouveautés disponibles
5 Tome 1 ce mois → 5 cartes affichées avec couverture, titre, date, éditeur.

#### Scenario: Aucune nouveauté
Aucune nouveauté prévue → message "Pas de nouveautés à venir pour cette période".

**Requirement:** The system SHALL display upcoming releases of first volumes (number=1), one-shots and guidebooks only.

### Requirement: Box Planning Display

Le système SHALL afficher les coffrets et éditions spéciales à venir (box_editions) avec couverture, titre, date, éditeur.

#### Scenario: Coffrets prévus
3 coffrets prévus ce mois → 3 cartes avec badge "Coffret" et infos complètes.

#### Scenario: Coffrets réservés
Utilisateur réserve un coffret → statut "Réservé" affiché et synchronisé.

**Requirement:** The system SHALL display upcoming boxes and special editions (box_editions) with cover, title, date, publisher.

### Requirement: Planning Extended Tabs

Le système SHALL ajouter les onglets "Nouveautés" et "Coffrets" au SubNavBar existant du Planning.

#### Scenario: Navigation onglets
Navigation entre 4 onglets : Personnalisé → Tout → Nouveautés → Coffrets.

#### Scenario: Cohérence données
Changement de filtre (éditeur/genre) affecte aussi Nouveautés et Coffrets.

**Requirement:** The system SHALL add "New Releases" and "Boxes" tabs to the existing Planning SubNavBar.
