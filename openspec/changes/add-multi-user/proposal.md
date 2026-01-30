# Change: Ajout du Multi-utilisateur (V4+)

## Why
Permettre un usage familial du Raspberry Pi avec des profils séparés pour chaque membre de la famille, chacun ayant sa propre collection et ses préférences sans interférence.

## What Changes
- Écran de sélection de profil au démarrage
- Création/suppression de profils utilisateur
- Isolation complète des données par profil (collection, préférences, cache, historique)
- Authentification séparée par profil via Auth API locale (JWT par utilisateur)
- Avatar personnalisé pour chaque profil

## Impact
- Affected specs: multi-user (new), authentication (modified for per-user JWT)
- Affected code: UserService, ProfileSelectorPage.qml, AuthService (extension), Auth API (multi-user support), app.py (startup flow)
- **BREAKING**: Le startup flow change pour afficher la sélection de profil
