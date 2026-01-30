# Tasks: Scanner code-barres

## 1. Infrastructure
- [ ] 1.1 Créer BluetoothScanner (bluetooth_scanner.py) - capture ISBN clavier
- [ ] 1.2 Créer WebcamScanner (webcam_scanner.py) - OpenCV + pyzbar
- [ ] 1.3 Créer ScannerService (scanner_service.py) - orchestration scan + recherche

## 2. Présentation
- [ ] 2.1 Créer ScannerViewModel
- [ ] 2.2 Créer ScannerPage.qml (preview caméra + résultat + actions)
- [ ] 2.3 Créer champ saisie manuelle ISBN

## 3. Intégration
- [ ] 3.1 Ajouter ScannerPage dans MainLayout
- [ ] 3.2 Connecter le scan à SearchService.search_by_isbn et CollectionService

## 4. Tests & Validation
- [ ] 4.1 Tests unitaires ScannerService (mock scanner)
- [ ] 4.2 Validation UI avec douchette réelle
