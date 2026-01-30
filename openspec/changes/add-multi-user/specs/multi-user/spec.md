# Capability: Multi-User (Gestion multi-utilisateur)

Système de profils utilisateur séparés avec isolation complète des données.

## ADDED Requirements

### Requirement: Profile Selection Screen

Le système SHALL afficher un écran de sélection de profil au démarrage avec avatars et noms.

#### Scenario: Premier lancement
- **GIVEN** l'application démarre pour la première fois
- **WHEN** aucun profil n'existe
- **THEN** le système affiche ProfileSelectorPage
- **AND** le système affiche un bouton "Créer un profil"
- **AND** le système n'affiche pas MainLayout

#### Scenario: Lancement avec profils existants
- **GIVEN** au moins un profil existe
- **WHEN** l'application démarre
- **THEN** le système affiche ProfileSelectorPage
- **AND** le système affiche une grille de cartes (une par profil)
- **AND** chaque carte affiche : avatar, nom, catégorie (optionnel)
- **AND** le système affiche un bouton "Créer un nouveau profil"

#### Scenario: Sélection d'un profil
- **GIVEN** plusieurs profils existent
- **WHEN** l'utilisateur clique sur un profil
- **THEN** le système affiche un indicateur de chargement
- **AND** le système charge les credentials depuis Auth API
- **AND** le système authenticate l'utilisateur avec JWT
- **AND** le système charge les données du profil (collection, cache)
- **AND** le système affiche MainLayout

#### Scenario: Erreur d'authentification du profil
- **GIVEN** l'utilisateur sélectionne un profil
- **WHEN** l'Auth API retourne une erreur
- **THEN** le système affiche un message "Erreur d'authentification"
- **AND** le système propose un bouton "Réessayer"
- **AND** le système reste sur ProfileSelectorPage

---

### Requirement: Profile Creation

Le système SHALL permettre de créer un nouveau profil avec nom d'utilisateur et credentials Mangacollec.

#### Scenario: Lancement du formulaire de création
- **GIVEN** l'utilisateur est sur ProfileSelectorPage
- **WHEN** il clique sur "Créer un profil"
- **THEN** le système affiche ProfileCreationForm
- **AND** le formulaire contient : nom, email, password (optionnel), credentials Mangacollec

#### Scenario: Création réussie
- **GIVEN** l'utilisateur remplit le formulaire
- **WHEN** il clique sur "Créer le profil"
- **THEN** le système valide les champs
- **AND** le système envoie POST /users à Auth API
- **AND** après succès, le profil est ajouté à la liste
- **AND** le système authenticate automatiquement le nouvel utilisateur
- **AND** le système affiche MainLayout

#### Scenario: Validation des champs
- **GIVEN** le formulaire est rempli
- **WHEN** l'utilisateur essaie de créer avec un champ vide
- **THEN** le système affiche un message d'erreur "Ce champ est obligatoire"
- **AND** le bouton "Créer" reste désactivé

#### Scenario: Credentials Mangacollec invalides
- **GIVEN** l'utilisateur entre des credentials Mangacollec incorrects
- **WHEN** il clique sur "Créer le profil"
- **THEN** le système valide les credentials
- **AND** si invalides, le système affiche "Credentials Mangacollec invalides"

#### Scenario: Nom de profil existant
- **GIVEN** un profil avec le même nom existe
- **WHEN** l'utilisateur essaie de créer
- **THEN** le système affiche "Ce nom de profil existe déjà"

---

### Requirement: Profile Deletion

Le système SHALL permettre de supprimer un profil avec confirmation et suppression des données associées.

#### Scenario: Suppression d'un profil
- **GIVEN** plusieurs profils existent
- **WHEN** l'utilisateur clique sur le menu d'un profil
- **AND** clique sur "Supprimer ce profil"
- **THEN** le système affiche une confirmation
- **AND** le message indique "Toutes les données de ce profil seront supprimées"
- **AND** le formulaire demande le mot de passe pour confirmation

#### Scenario: Confirmation de suppression
- **GIVEN** la confirmation est affichée
- **WHEN** l'utilisateur entre le mot de passe et clique "Supprimer"
- **THEN** le système supprime le profil depuis Auth API
- **AND** le système supprime les données locales (cache, preferences)
- **AND** le système rafraîchit ProfileSelectorPage
- **AND** le profil n'est plus visible

#### Scenario: Annulation de suppression
- **GIVEN** la confirmation est affichée
- **WHEN** l'utilisateur clique "Annuler"
- **THEN** le système ferme le formulaire
- **AND** le profil reste intact

#### Scenario: Suppression du dernier profil
- **GIVEN** un seul profil existe
- **WHEN** l'utilisateur tente de le supprimer
- **THEN** le système affiche un message "Vous devez avoir au moins un profil"
- **AND** le bouton "Supprimer" est désactivé

---

### Requirement: Data Isolation

Le système SHALL isoler complètement les données entre profils : collection, préférences, cache, historique.

#### Scenario: Isolation de la collection
- **GIVEN** profil A possède 50 volumes
- **AND** profil B possède 20 volumes
- **WHEN** l'utilisateur sélectionne profil A
- **THEN** la collection affiche exactement 50 volumes
- **WHEN** l'utilisateur switch vers profil B
- **THEN** la collection affiche exactement 20 volumes
- **AND** aucune donnée du profil A n'est visible

#### Scenario: Isolation des préférences
- **GIVEN** profil A a défini des préférences (thème, langue, etc.)
- **AND** profil B a d'autres préférences
- **WHEN** l'utilisateur switch entre profils
- **THEN** les préférences correctes sont appliquées à chaque profil

#### Scenario: Isolation du cache
- **GIVEN** profil A a un cache de volumes chargés
- **WHEN** l'utilisateur switch vers profil B
- **THEN** le cache du profil B est utilisé (données séparées)
- **AND** le cache du profil A n'est pas accessible

#### Scenario: Isolation de l'historique
- **GIVEN** profil A a un historique de recherches
- **WHEN** l'utilisateur est sur profil B
- **THEN** l'historique du profil A n'apparaît pas
- **AND** le profil B affiche son propre historique

---

### Requirement: Profile Switching

Le système SHALL permettre de changer de profil sans redémarrer l'application.

#### Scenario: Accès au menu de changement de profil
- **GIVEN** l'utilisateur est sur MainLayout
- **WHEN** il clique sur "Changer de profil" (menu ou avatar)
- **THEN** le système affiche ProfileSelectorPage
- **AND** le profil actuel est indiqué/surligné

#### Scenario: Switch de profil
- **GIVEN** ProfileSelectorPage est affichée
- **WHEN** l'utilisateur sélectionne un autre profil
- **THEN** le système se déconnecte de l'utilisateur actuel
- **AND** le système charge les données du nouveau profil
- **AND** le système réaffiche MainLayout avec les données du nouveau profil
- **AND** le temps total de switch est < 2 secondes

#### Scenario: Logout et retour à la sélection
- **GIVEN** l'utilisateur est sur MainLayout
- **WHEN** il sélectionne "Logout" ou "Quitter"
- **THEN** le système retourne à ProfileSelectorPage
- **AND** le profil actuel n'est pas pré-sélectionné (pour sécurité)

---

### Requirement: Profile Editing

Le système SHALL permettre de modifier les informations d'un profil (sauf les credentials Mangacollec pour V4).

#### Scenario: Édition du profil actuel
- **GIVEN** l'utilisateur est sur MainLayout
- **WHEN** il accède aux paramètres du profil
- **THEN** le système affiche ProfileEditForm
- **AND** les champs actuels sont pré-remplis : nom, email, avatar

#### Scenario: Modification du nom
- **GIVEN** ProfileEditForm est affichée
- **WHEN** l'utilisateur modifie le nom
- **AND** clique "Enregistrer"
- **THEN** le système envoie PUT /users/{id} à Auth API
- **AND** après succès, le nouveau nom est affiché
- **AND** ProfileSelectorPage affiche le nom mis à jour

#### Scenario: Changement d'avatar
- **GIVEN** ProfileEditForm est affichée
- **WHEN** l'utilisateur clique sur l'avatar
- **THEN** le système ouvre un sélecteur d'avatar (prédéfinis ou upload)
- **AND** après sélection, l'avatar est mis à jour
