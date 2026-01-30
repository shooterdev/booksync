# Spec: Historique Collection et Lecture

## ADDED Requirements

### Requirement: Collection History Journal

Le système SHALL afficher un journal chronologique des volumes ajoutés à la collection, groupés par année puis par mois.

#### Scenario: Historique collection
5 ajouts en janvier 2025 → section "Janvier 2025" avec 5 entrées classées par date décroissante.

#### Scenario: Multiple années
Ajouts 2023, 2024, 2025 → 3 sections année collapsibles, décroissant.

**Requirement:** The system SHALL display a chronological journal of volumes added to the collection, grouped by year then by month.

### Requirement: Reading History Journal

Le système SHALL afficher un journal chronologique des lectures effectuées, groupées par semaine/mois/année.

#### Scenario: Historique lecture semaine
3 lectures cette semaine → section "Cette semaine" avec 3 entrées horodatées.

#### Scenario: Groupage temporel
Lectures réparties sur 3 mois → 3 sections mois avec entrées.

**Requirement:** The system SHALL display a chronological journal of readings performed, grouped by week/month/year.

### Requirement: History Entry Display

Le système SHALL afficher chaque entrée avec : couverture, titre série, numéro du tome, date.

#### Scenario: Entrée d'ajout
Couverture + "One Piece T.5" + "15 jan 2025" + bouton accès fiche.

#### Scenario: Entrée de lecture
Couverture + "Jujutsu Kaisen T.3" + "22 jan 2025 (date de lecture)" + statut "lu".

**Requirement:** The system SHALL display each entry with: cover, series title, volume number, date.

### Requirement: History Navigation

Le système SHALL permettre de naviguer vers la fiche volume depuis une entrée d'historique.

#### Scenario: Accès fiche
Clic sur entrée → ouverture fiche volume dans panneau détail ou page dédiée.

#### Scenario: Retour historique
Retour depuis fiche → retour à l'historique avec scroll position préservé.

**Requirement:** The system SHALL allow navigation to the volume record from a history entry.
