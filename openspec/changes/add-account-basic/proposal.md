# Change: Ajout de la gestion du compte basique (V1)

## Why

L'utilisateur doit pouvoir consulter son profil, accéder à ses paramètres personnels, et gérer des préférences de base comme le thème visuel et le filtrage du contenu adulte. La gestion du compte est essentielle pour offrir une expérience personnalisée et permet également la déconnexion sécurisée.

## What Changes

### Nouveaux fichiers/composants

**Services:**
- `UserService` — gestion profil utilisateur et préférences
- `PreferencesManager` — persistance des préférences en TinyDB ou QSettings
- Extension `AuthService` — méthode logout

**Présentation:**
- `UserViewModel` — gestion données profil et préférences
- Composants QML: `SettingsPage.qml`, extension `Theme.qml` pour toggle clair/sombre
- Dialogs: `LogoutConfirmationDialog.qml`

**Intégration:**
- Ajout entrée "Paramètres" ou "Compte" dans `SideBar.qml`
- Système de thème global avec sauvegarde des préférences

## Impact

- **Affected specs**: Nouvelle capability `account` (aucune spec existante)
- **Affected code**:
  - `booksync_app_qt/src/services/` — UserService, extension AuthService
  - `booksync_app_qt/src/viewmodels/` — UserViewModel
  - `booksync_app_qt/src/managers/` — PreferencesManager
  - `booksync_app_qt/qml/pages/` — SettingsPage.qml
  - `booksync_app_qt/qml/theme/` — Theme.qml (extension)
  - `SideBar.qml` — ajout entrée Paramètres
  - MainLayout.qml — intégration SettingsPage
- **Breaking changes**: Aucun

## Scope

Cette proposition couvre:
- Affichage profil utilisateur (nom, email)
- Toggle thème clair/sombre avec persistance
- Toggle affichage contenu adulte avec filtrage
- Bouton de déconnexion avec confirmation
- Persistance des préférences au redémarrage

**Hors scope** (futures versions):
- Modification du profil (nom, email, avatar)
- Authentification à deux facteurs
- Notifications et alertes
- Gestion avancée des données privées
