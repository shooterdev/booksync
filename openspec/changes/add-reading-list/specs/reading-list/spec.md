# Spec: Pile à lire

## ADDED Requirements

### Requirement: Reading Progress Display

Le système SHALL afficher une barre de statut (StatusRead) avec le nombre de volumes lus sur le total possédé et un pourcentage de progression globale.

#### Scenario: Affichage progression
45 lus sur 120 possédés → "45/120 lus (37%)"

**Requirement:** The system SHALL display a status bar (StatusRead) with the number of volumes read out of the total owned and a percentage of overall progress.

### Requirement: Series Reading List

Le système SHALL afficher la liste des séries en cours de lecture avec couverture, titre, barre de progression (lus/possédés), prochain tome à lire.

#### Scenario: Liste séries en cours
3 séries en cours → 3 cartes SerieReadCard affichées avec infos complètes.

**Requirement:** The system SHALL display the list of series currently being read with cover, title, progress bar (read/owned), and next volume to read.

### Requirement: Mark Volume as Read

Le système SHALL permettre de marquer un volume comme lu via l'API Mangacollec (POST reads).

#### Scenario: Marquer tome comme lu
Marquer tome 5 comme lu → progression série mise à jour immédiatement.

#### Scenario: Validation API
Appel API POST reads réussi → mise en cache local + UI refresh.

**Requirement:** The system SHALL allow marking a volume as read via the Mangacollec API (POST reads).

### Requirement: Pause and Complete Reading

Le système SHALL permettre de mettre en pause ou terminer la lecture d'une série (via read_editions).

#### Scenario: Pause série
Pause → série déplacée en bas de liste avec statut "En pause".

#### Scenario: Terminer lecture
Terminer → série retirée de la pile et archivée.

**Requirement:** The system SHALL allow pausing or completing the reading of a series (via read_editions).
