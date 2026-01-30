## 1. Service
- [ ] 1.1 Créer PredictionService (prediction_service.py) - communication Prediction API
- [ ] 1.2 Implémenter appel POST /predict avec QCM + humeur
- [ ] 1.3 Implémenter récupération historique GET /predictions/{user_id}
- [ ] 1.4 Implémenter gestion d'erreur et retry automatique

## 2. Présentation
- [ ] 2.1 Créer PredictionViewModel
- [ ] 2.2 Créer PredictionPage.qml (écran principal)
- [ ] 2.3 Créer QcmPage.qml (questionnaire)
- [ ] 2.4 Créer MoodSelector.qml (sélection humeur)
- [ ] 2.5 Créer PredictionResultCard.qml (résultat suggestion)
- [ ] 2.6 Créer PredictionHistoryList.qml
- [ ] 2.7 Ajouter l'icône/bouton "Obtenir une suggestion" dans MainLayout

## 3. Intégration
- [ ] 3.1 Ajouter PredictionPage dans MainLayout et SideBar
- [ ] 3.2 Connecter la suggestion à la navigation fiche volume
- [ ] 3.3 Implémenter le bouton "Autre suggestion"

## 4. Tests & Validation
- [ ] 4.1 Tests unitaires PredictionService (mock API)
- [ ] 4.2 Tests unitaires PredictionViewModel
- [ ] 4.3 Validation UI complète
- [ ] 4.4 Test de l'historique en persistance
