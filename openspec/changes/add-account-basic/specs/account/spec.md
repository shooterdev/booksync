# Capability: Account (User Profile & Settings)

## Purpose

La fonctionnalité Account permet aux utilisateurs de consulter leur profil, de gérer des préférences personnelles simples (thème visuel, affichage du contenu adulte), et de se déconnecter de l'application de manière sécurisée. Elle offre une expérience personnalisée et renforce la sécurité de la session utilisateur.

---

## ADDED Requirements

### Requirement: User Profile Display

The system SHALL display the current user's profile (username, email) on the Account page.

#### Scenario: Affichage profil utilisateur

- **GIVEN** un utilisateur authentifié accédant à la page Paramètres
- **WHEN** la page se charge
- **THEN** le système affiche: nom d'utilisateur, email
- **AND** les informations sont non-éditables et en lecture seule
- **AND** les données proviennent de la session authentifiée

#### Scenario: Profil utilisateur indisponible

- **GIVEN** un utilisateur accédant à la page Paramètres
- **WHEN** les données utilisateur ne peuvent pas être récupérées
- **THEN** le système affiche un message d'erreur "Impossible de charger le profil"
- **AND** une action "Réessayer" est proposée

#### Scenario: Affichage du profil après connexion

- **GIVEN** un utilisateur qui vient de se connecter
- **WHEN** il accède à la page Paramètres
- **THEN** son profil est immédiatement visible
- **AND** les données sont à jour

---

### Requirement: Theme Toggle

The system SHALL allow users to switch between light and dark themes via a toggle switch.

#### Scenario: Basculement thème clair → sombre

- **GIVEN** un utilisateur en mode thème clair
- **WHEN** l'utilisateur active le switch "Thème sombre"
- **THEN** toute l'interface bascule immédiatement au thème sombre
- **AND** les couleurs de fond, texte, et éléments changent
- **AND** une transition visuelle fluide est visible

#### Scenario: Basculement thème sombre → clair

- **GIVEN** un utilisateur en mode thème sombre
- **WHEN** l'utilisateur désactive le switch
- **THEN** l'interface revient au thème clair
- **AND** la transition est fluide et instantanée

#### Scenario: Persistance du thème

- **GIVEN** un utilisateur ayant sélectionné le thème sombre
- **WHEN** l'utilisateur ferme et relance l'application
- **THEN** le thème sombre est restauré
- **AND** aucune action de reconfiguration n'est requise

#### Scenario: Thème par défaut

- **GIVEN** un nouvel utilisateur accédant à l'application
- **WHEN** le profil est créé et l'app s'ouvre pour la première fois
- **THEN** le thème clair est appliqué par défaut

#### Scenario: Changement thème affecte toutes les pages

- **GIVEN** un utilisateur naviguant dans l'application en thème clair
- **WHEN** l'utilisateur change vers le thème sombre depuis Paramètres
- **THEN** toutes les pages (Collection, Recherche, Planning, etc.) s'affichent en thème sombre
- **AND** le changement est cohérent dans toute l'app

---

### Requirement: Adult Content Toggle

The system SHALL allow users to mask/display adult content via a toggle switch.

#### Scenario: Activation du filtrage contenu adulte

- **GIVEN** un utilisateur sur la page Paramètres
- **WHEN** le switch "Afficher contenu adulte" est désactivé
- **THEN** les séries et volumes marqués comme contenu adulte sont masqués
- **AND** cela s'applique aux pages Collection, Recherche, Planning, fiches Auteur/Éditeur

#### Scenario: Désactivation du filtrage contenu adulte

- **GIVEN** un utilisateur ayant activé le filtrage
- **WHEN** l'utilisateur réactive le switch
- **THEN** le contenu adulte redevient visible dans toute l'app
- **AND** les résultats de recherche incluent à nouveau les séries adultes

#### Scenario: Persistance du filtre contenu adulte

- **GIVEN** un utilisateur ayant désactivé l'affichage contenu adulte
- **WHEN** l'utilisateur ferme et relance l'application
- **THEN** le filtre est maintenu
- **AND** les séries adultes restent masquées

#### Scenario: Filtrage Collection

- **GIVEN** un utilisateur avec séries adultes dans sa collection
- **WHEN** le filtre contenu adulte est activé
- **THEN** les volumes adultes disparaissent de la vue Collection
- **AND** la progression des séries est recalculée sans les tomes adultes

#### Scenario: Filtrage Recherche

- **GIVEN** un utilisateur ayant le filtre contenu adulte actif
- **WHEN** il effectue une recherche
- **THEN** les résultats n'incluent pas les séries/volumes adultes
- **AND** le nombre de résultats peut diminuer selon le filtre

---

### Requirement: Logout

The system SHALL allow users to logout securely with confirmation.

#### Scenario: Déconnexion via bouton

- **GIVEN** un utilisateur sur la page Paramètres
- **WHEN** l'utilisateur clique sur le bouton "Déconnexion"
- **THEN** une dialog de confirmation s'affiche
- **AND** deux options sont proposées: "Annuler" et "Déconnexion"

#### Scenario: Confirmation de déconnexion

- **GIVEN** une dialog de confirmation de déconnexion affichée
- **WHEN** l'utilisateur confirme "Déconnexion"
- **THEN** les tokens d'authentification sont supprimés
- **AND** la session utilisateur est fermée
- **AND** l'application retourne à l'écran de connexion
- **AND** aucune donnée sensible ne reste en mémoire

#### Scenario: Annulation de déconnexion

- **GIVEN** une dialog de confirmation affichée
- **WHEN** l'utilisateur clique "Annuler"
- **THEN** la dialog se ferme
- **AND** l'utilisateur reste connecté
- **AND** la page Paramètres reste affichée

#### Scenario: Logout avec erreur réseau

- **GIVEN** un utilisateur tentant de se déconnecter
- **WHEN** une erreur réseau survient durant le logout
- **THEN** un message d'erreur s'affiche "Déconnexion échouée"
- **AND** une action "Réessayer" est proposée
- **AND** l'utilisateur peut également forcer la déconnexion localement

#### Scenario: Verrouillage session après logout

- **GIVEN** un utilisateur qui vient de se déconnecter
- **WHEN** l'application quitte la session
- **THEN** les données sensibles sont purgées
- **AND** la nouvelle connexion requiert l'authentification complète (identifiants)

#### Scenario: Navigation après logout échouée

- **GIVEN** un utilisateur dont la déconnexion a échouée
- **WHEN** l'utilisateur ferme l'application
- **THEN** l'app se relance
- **AND** un message propose de relancer la déconnexion ou continue normalement
