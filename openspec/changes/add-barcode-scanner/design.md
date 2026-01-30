# Design: Scanner code-barres

## Context
Deux modes de scan : douchette Bluetooth (émulation clavier, envoie l'ISBN suivi d'Enter) et webcam (OpenCV + pyzbar). Le Raspberry Pi dispose d'USB pour douchette et de caméra CSI/USB. Écran tactile 1280x720.

## Decisions
- Douchette Bluetooth : capture du focus clavier globale, détection pattern ISBN (13 chiffres + Enter)
- Webcam : OpenCV VideoCapture + pyzbar decode en boucle, limité à 5 FPS pour performance RPi
- Après scan : recherche API par ISBN → affichage fiche volume → confirmation ajout → ajout collection
- Mode fallback saisie manuelle ISBN si douchette échoue ou absence de webcam

## Non-Goals
- Pas de scan NFC
- Pas de reconnaissance de couverture par image (trop gourmand en CPU)
- Pas de support multi-ISBN (un par scan)

## Risks
- Performance webcam sur RPi → limiter à 5 FPS pour éviter lag UI
- Douchette non détectée au démarrage → mode saisie manuelle en fallback
- Faux positifs pyzbar → validation ISBN checksum avant recherche
- Connexion API perte → cache offline des derniers scans
