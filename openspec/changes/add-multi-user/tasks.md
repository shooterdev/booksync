## 1. Backend (Auth API)
- [ ] 1.1 Étendre Auth API - ajouter table users si n'existe pas
- [ ] 1.2 Créer endpoints : POST /users, GET /users, PUT /users/{id}, DELETE /users/{id}
- [ ] 1.3 Implémenter JWT par utilisateur avec expiration 24h
- [ ] 1.4 Implémenter refresh token endpoint
- [ ] 1.5 Ajouter validation: un utilisateur par credentials Mangacollec

## 2. Service
- [ ] 2.1 Créer UserService (user_service.py) - gestion profils
- [ ] 2.2 Étendre AuthService - login/logout par profil, token management
- [ ] 2.3 Implémenter partitionnement du cache TinyDB par user_id
- [ ] 2.4 Implémenter partitionnement des preferences par user_id
- [ ] 2.5 Créer fonction de migration des données au premier lancement

## 3. Présentation
- [ ] 3.1 Créer ProfileSelectorPage.qml (écran sélection profil avec avatars)
- [ ] 3.2 Créer ProfileCreationForm.qml (formulaire création profil)
- [ ] 3.3 Créer ProfileEditForm.qml (édition profil)
- [ ] 3.4 Créer UserViewModel (QAbstractListModel)
- [ ] 3.5 Modifier app.py pour afficher ProfileSelector au démarrage
- [ ] 3.6 Ajouter bouton "Changer de profil" dans MainLayout (menu ou SideBar)

## 4. Intégration
- [ ] 4.1 Router: ProfileSelector comme écran initial, MainLayout après authentification
- [ ] 4.2 Implémenter logout et retour à ProfileSelector
- [ ] 4.3 Implémenter suppression de profil avec confirmation et cleanup données
- [ ] 4.4 Implémenter persistance du profil actuel pour le redémarrage rapide

## 5. Migration
- [ ] 5.1 Créer script de migration des données existantes vers profil "default"
- [ ] 5.2 Tester la migration sur une installation existante
- [ ] 5.3 Documenter la procédure pour utilisateurs

## 6. Tests & Validation
- [ ] 6.1 Tests unitaires UserService (CRUD)
- [ ] 6.2 Tests intégration Auth API multi-user et JWT
- [ ] 6.3 Tests de partitionnement du cache par user_id
- [ ] 6.4 Tests de suppression de profil et cleanup
- [ ] 6.5 Validation UI complète : création, édition, suppression, switch
- [ ] 6.6 Test de performance : switch de profil < 2 secondes
