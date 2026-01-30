# Tasks: Account Basic (Profile, Settings, Logout) V1

## 1. Service

- [ ] 1.1 Créer `UserService` dans `services/user_service.py`
  - Méthode `get_current_user()` — retourne profil de l'utilisateur (id, username, email)
  - Méthode `get_user_preferences()` — récupère les préférences depuis PreferencesManager
  - Méthode `update_user_preferences(theme, adult_content_enabled)` — met à jour les préfs
  - Intégration avec AuthService pour accéder à l'utilisateur courant

- [ ] 1.2 Créer `PreferencesManager` dans `managers/preferences_manager.py`
  - Stockage en TinyDB (ou QSettings si plus simple)
  - Méthode `load_preferences()` — charge prefs de la DB
  - Méthode `save_preferences(prefs)` — sauvegarde prefs
  - Clés: `theme` (light|dark), `show_adult_content` (bool)
  - Valeurs par défaut: theme=light, show_adult_content=false

- [ ] 1.3 Étendre `AuthService` dans `services/auth_service.py`
  - Méthode `logout()` — supprime tokens, nettoie session, émet signal logout
  - Méthode `is_authenticated()` — retourne booléen
  - Signal `loggedOut()` pour notifier l'application

## 2. Présentation (ViewModel)

- [ ] 2.1 Créer `UserViewModel` dans `viewmodels/user_viewmodel.py`
  - Properties: `username` (str), `email` (str), `currentTheme` (light|dark), `adultContentEnabled` (bool)
  - Méthode `load_user_profile()` — récupère profil utilisateur
  - Méthode `set_theme(theme: str)` — bascule thème et persisté
  - Méthode `set_adult_content(enabled: bool)` — bascule filtre contenu adulte
  - Méthode `logout()` — délègue à AuthService et émet signal
  - Signal `profileLoaded()`, `themeChanged(theme)`, `adultContentChanged()`, `logoutRequested()`, `errorOccurred(message)`

## 3. Système de Thème

- [ ] 3.1 Étendre `Theme.qml` dans `qml/theme/`
  - Ajouter property `isDark` (bool) — état du thème
  - Ajouter property `accentColor`, `backgroundColor`, `textColor` (liées à isDark)
  - Créer fonction `toggleTheme()` — bascule isDark et notifie UserViewModel
  - Charger le thème sauvegardé au démarrage de l'app

- [ ] 3.2 Intégrer Theme.qml dans `main.qml` ou `App.qml`
  - Rendre accessible globalement via `root.theme` ou singleton
  - Appliquer les couleurs de thème à tous les composants

## 4. Interface (QML)

- [ ] 4.1 Créer `SettingsPage.qml` dans `qml/pages/`
  - En-tête "Paramètres" ou "Compte"
  - Section Profil:
    - Affichage non-éditable: Nom d'utilisateur, Email
    - Message si données indisponibles
  - Section Préférences:
    - Switch "Thème sombre" — bascule theme et persiste
    - Switch "Afficher contenu adulte" — active/désactive filtre
  - Section Actions:
    - Bouton "Déconnexion" rouge/accent
  - Message d'erreur en cas de problème chargement

- [ ] 4.2 Créer `LogoutConfirmationDialog.qml` dans `qml/dialogs/`
  - Titre: "Confirmation de déconnexion"
  - Message: "Êtes-vous sûr de vouloir vous déconnecter?"
  - 2 boutons: "Annuler", "Déconnexion"
  - Sur confirmation → appelle `UserViewModel.logout()`

- [ ] 4.3 Intégrer `SettingsPage` dans `MainLayout.qml`
  - Ajouter à la stack de navigation
  - Breadcrumb: Home > Paramètres

## 5. Intégration

- [ ] 5.1 Ajouter entrée "Paramètres" ou "Compte" dans `SideBar.qml`
  - Icône (engrenage/user)
  - Click → navigation vers SettingsPage

- [ ] 5.2 Intégrer UserViewModel dans `main.qml` ou `App.qml`
  - Instancier au démarrage
  - Connecter signal `loggedOut()` → retour écran login

- [ ] 5.3 Connecter signal `themeChanged()` → mise à jour Theme global
  - Appliquer les couleurs du nouveau thème à toute l'app
  - Transition visuelle fluide

- [ ] 5.4 Intégrer filtrage contenu adulte dans les ViewModels affectés
  - Collection, Recherche, Planning, Détails Série
  - Filtrer les résultats si `adultContentEnabled` = false

## 6. Tests

- [ ] 6.1 Tests unitaires `UserService`
  - Test `get_current_user()` — retourne objet avec nom et email
  - Test `get_user_preferences()` — retourne prefs
  - Test `update_user_preferences()` — persistance OK

- [ ] 6.2 Tests unitaires `PreferencesManager`
  - Test sauvegarde et chargement thème
  - Test sauvegarde et chargement content adulte
  - Test valeurs par défaut

- [ ] 6.3 Tests unitaires `UserViewModel`
  - Test chargement profil
  - Test basculement thème avec persistance
  - Test basculement contenu adulte
  - Test signal logout

- [ ] 6.4 Tests intégration logout
  - Test suppression tokens après logout
  - Test retour écran login

## 7. Validation

- [ ] 7.1 Lancement application, vérification logs console (aucune erreur QML)
- [ ] 7.2 Navigation SideBar → Paramètres fonctionnelle
- [ ] 7.3 Profil utilisateur s'affiche correctement (nom, email)
- [ ] 7.4 Switch thème sombre → couleurs changent immédiatement
- [ ] 7.5 Thème persiste au redémarrage de l'app
- [ ] 7.6 Switch contenu adulte → filtre appliqué dans Collection/Recherche
- [ ] 7.7 Clic bouton Déconnexion → dialog confirmation s'affiche
- [ ] 7.8 Confirmation déconnexion → logout + retour login
- [ ] 7.9 Annulation déconnexion → reste sur SettingsPage
- [ ] 7.10 Responsive design sur 1280x720 (écran tactile)
- [ ] 7.11 Capture screenshot de SettingsPage pour documentation
