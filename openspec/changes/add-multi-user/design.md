## Context
L'Auth API locale (port 8000) gère l'authentification multi-utilisateur avec JWT. Chaque profil a ses propres credentials Mangacollec stockés côté serveur. L'app Qt doit se réauthentifier pour chaque changement de profil.

## Goals
- Support d'au moins 5 profils par Raspberry Pi
- Isolation totale des données entre profils
- Switch de profil sans redémarrage < 2 secondes
- Cache partitionné par profil

## Non-Goals
- Pas de contrôle parental (V5+)
- Pas de partage de collection entre profils
- Pas de sync des profils entre appareils
- Pas de migration automatique des données existantes (script séparé)

## Decisions
- Profils stockés dans Auth API (PostgreSQL table users)
- Chaque profil a un token JWT distinct valide 24h
- Cache TinyDB partitionné par user_id : `cache_<user_id>.json`
- Preferences stockées par profil : `preferences_<user_id>.json`
- Avatar stocké en base64 ou fichier local par profil
- Le premier lancement affiche la création de profil (pas de login auto)

## Risks
- Auth API indisponible → seul le dernier profil utilisé est accessible via cache local
- Migration des données existantes → script de migration pour le profil actuel
- Performance TinyDB avec 5+ profils → index sur user_id obligatoire

## Migration Plan
- Créer un profil "default" au premier lancement
- Migrer les données existantes (TinyDB) vers ce profil
- Importer les credentials Mangacollec actuels
- Afficher un message "Migration réussie - Vous pouvez créer d'autres profils"
