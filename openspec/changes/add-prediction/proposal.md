# Change: Ajout des Recommandations IA (V3)

## Why
L'utilisateur veut recevoir des suggestions de lecture personnalisées lorsqu'il ne sait pas quoi lire, basées sur ses préférences du moment et son humeur.

## What Changes
- Ajout d'un QCM de préférences rapide (genres, thèmes du moment)
- Sélection d'humeur (joyeux, mélancolique, stressé, curieux, énergique, détendu)
- Algorithme de suggestion basé sur les réponses QCM + humeur + embeddings (pgvector + sentence-transformers)
- Historique des prédictions passées
- Communication avec la Prediction API locale (port 8002)

## Impact
- Affected specs: prediction (new)
- Affected code: PredictionService, PredictionViewModel, PredictionPage.qml, QcmPage.qml, MoodSelector.qml, PredictionAPI (booksync_api_prediction)
