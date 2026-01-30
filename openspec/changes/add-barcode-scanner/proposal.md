# Change: Ajout du Scanner code-barres (V2)

## Why
L'ajout rapide de tomes via douchette Bluetooth (ISBN) ou webcam permet une gestion efficace de la collection physique sans recherche manuelle.

## What Changes
- Support douchette Bluetooth (émule clavier, saisit l'ISBN)
- Support webcam via OpenCV + pyzbar pour lecture code-barres
- Recherche automatique du volume par ISBN via l'API
- Ajout direct à la collection après confirmation

## Impact
- Affected specs: barcode-scanner (new)
- Affected code: ScannerService, BluetoothScanner, WebcamScanner, ScannerPage.qml
