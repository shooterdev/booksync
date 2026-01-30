# Spec: Scanner code-barres

## ADDED Requirements

### Requirement: Bluetooth Scanner Input

Le système SHALL détecter la saisie d'un ISBN via douchette Bluetooth (émulation clavier, 13 chiffres + Enter).

#### Scenario: Scan succès
Scan ISBN 9782505085812 → reconnaissance pattern → recherche automatique déclenchée.

#### Scenario: Saisie invalide
Saisie 12345 (5 chiffres) → message "ISBN non reconnu, format invalide (13 chiffres requis)".

#### Scenario: ISBN avec checksum
Validation checksum ISBN-13 avant recherche → ISBN invalide → message "Checksum invalide".

**Requirement:** The system SHALL detect ISBN input via Bluetooth barcode scanner (keyboard emulation, 13 digits + Enter).

### Requirement: Webcam Scanner

Le système SHALL permettre le scan de code-barres via webcam en utilisant OpenCV et pyzbar.

#### Scenario: Détection code-barres
Présenter un manga devant la caméra → code-barres détecté → ISBN extrait → recherche.

#### Scenario: Code-barres illisible
Code-barres endommagé → message "Code-barres illisible, réessayez" avec aperçu caméra.

#### Scenario: Performance RPi
OpenCV limité à 5 FPS pour éviter lag UI sur Raspberry Pi.

**Requirement:** The system SHALL allow barcode scanning via webcam using OpenCV and pyzbar.

### Requirement: ISBN Search and Add

Le système SHALL rechercher le volume par ISBN via l'API Mangacollec et proposer l'ajout direct à la collection.

#### Scenario: Volume trouvé
ISBN trouvé → fiche volume affichée (couverture, titre, éditeur, prix) → bouton "Ajouter à la collection".

#### Scenario: Volume non trouvé
ISBN non trouvé dans catalogue → message "Volume non trouvé dans le catalogue Mangacollec. Recherche manuelle?".

#### Scenario: Ajout confirmation
Clic "Ajouter" → ajout à la collection → notification succès → retour scanner.

**Requirement:** The system SHALL search for the volume by ISBN via the Mangacollec API and propose direct collection addition.

### Requirement: Manual ISBN Entry

Le système SHALL permettre la saisie manuelle d'un ISBN en fallback.

#### Scenario: Saisie manuelle
Champ texte ISBN → saisie 9782505085812 → bouton "Chercher" → même processus que scan.

#### Scenario: Historique saisies
Derniers ISBNs saisis → liste déroulante pour réitérer rapidement.

**Requirement:** The system SHALL allow manual ISBN entry as a fallback.
