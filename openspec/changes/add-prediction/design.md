## Context
La Prediction API est un microservice FastAPI sur le serveur/NAS qui utilise pgvector (PostgreSQL) et sentence-transformers pour générer des embeddings de séries manga. L'app Qt envoie les réponses QCM et l'humeur à l'API qui retourne une suggestion de tome depuis la pile à lire de l'utilisateur.

## Goals
- Suggérer un tome pertinent depuis la pile à lire
- Prendre en compte les préférences du moment (pas seulement l'historique)
- Temps de réponse < 3 secondes

## Non-Goals
- Pas de suggestion de séries non possédées (c'est un outil de lecture, pas de découverte)
- Pas d'entraînement du modèle côté client

## Decisions
- L'app envoie un payload {qcm_answers, mood, user_id} à POST /predict
- L'API retourne {suggested_volume_id, confidence, reasoning}
- Les embeddings sont précalculés côté serveur
- L'historique est stocké côté API (table predictions)

## Risks
- Prediction API indisponible → message "Service indisponible, réessayez plus tard"
- Suggestion non pertinente → bouton "Autre suggestion" pour relancer
- Performance dégradée si les embeddings ne sont pas précalculés → imposer un refresh nightly
