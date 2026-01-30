# Capability: Prediction (Recommandations IA)

Système de recommandations de lecture basé sur les préférences du moment et l'humeur de l'utilisateur.

## ADDED Requirements

### Requirement: Preference QCM

Le système SHALL afficher un questionnaire rapide sur les préférences du moment avec au minimum 3 questions couvrant : genres préférés, thèmes souhaités, longueur souhaitée.

#### Scenario: Affichage du QCM
- **GIVEN** l'utilisateur est sur la PredictionPage
- **WHEN** l'utilisateur clique sur "Répondre à nos questions"
- **THEN** le système affiche la page QCM
- **AND** le système affiche 3-5 questions avec des réponses multiples

#### Scenario: Réponses du QCM
- **GIVEN** l'utilisateur remplit le QCM
- **WHEN** l'utilisateur confirme ses réponses
- **THEN** le système stocke les réponses en mémoire
- **AND** le système procède à la sélection d'humeur

#### Scenario: Skip QCM
- **GIVEN** l'utilisateur est sur la PredictionPage
- **WHEN** l'utilisateur clique sur "Obtenir une suggestion"
- **THEN** le système skip le QCM (utilise les préférences par défaut du profil)
- **AND** le système procède directement à la sélection d'humeur

---

### Requirement: Mood Selection

Le système SHALL afficher une sélection d'humeur parmi une liste prédéfinie : joyeux, mélancolique, stressé, curieux, énergique, détendu.

#### Scenario: Sélection d'humeur
- **GIVEN** l'utilisateur a répondu au QCM (ou l'a skippé)
- **WHEN** la MoodSelector est affichée
- **THEN** le système affiche 6 boutons (un par humeur)
- **AND** chaque bouton affiche une icône ou couleur distincte

#### Scenario: Confirmation d'humeur
- **GIVEN** l'utilisateur a sélectionné une humeur
- **WHEN** l'utilisateur clique sur le bouton "Obtenir la suggestion"
- **THEN** le système envoie le payload {qcm_answers, mood, user_id} à POST /predict

---

### Requirement: Reading Prediction

Le système SHALL afficher la suggestion de lecture retournée par la Prediction API avec : couverture du volume, titre série, numéro du tome, raison de la suggestion.

#### Scenario: Affichage de la suggestion réussie
- **GIVEN** l'API Prediction retourne une suggestion valide
- **WHEN** la suggestion est reçue
- **THEN** le système affiche un indicateur de chargement durant l'appel
- **AND** le système affiche une carte PredictionResultCard avec :
  - Couverture du volume
  - Titre de la série
  - Numéro du tome (ex: "Tome 5")
  - Raison de la suggestion (reasoning)
  - Indicateur de confiance (optionnel)

#### Scenario: API indisponible
- **GIVEN** l'API Prediction est indisponible ou timeout
- **WHEN** l'appel échoue
- **THEN** le système affiche un message "Service indisponible, réessayez plus tard"
- **AND** le système propose un bouton "Réessayer"

#### Scenario: Aucune suggestion disponible
- **GIVEN** la pile à lire est vide
- **WHEN** l'API retourne un code 404 ou liste vide
- **THEN** le système affiche un message "Vous avez fini votre pile à lire ! Ajoutez de nouveaux volumes."

---

### Requirement: Alternative Suggestion

Le système SHALL permettre de demander une autre suggestion si la première ne convient pas.

#### Scenario: Demand d'une autre suggestion
- **GIVEN** une suggestion est affichée
- **WHEN** l'utilisateur clique sur le bouton "Autre suggestion"
- **THEN** le système envoie un nouvel appel API avec les mêmes paramètres
- **AND** le système affiche un nouvel indicateur de chargement
- **AND** le système affiche la nouvelle suggestion (ou un message si aucune autre n'existe)

#### Scenario: Limite de suggestions atteinte
- **GIVEN** l'utilisateur a déjà reçu 5 suggestions pour cette session
- **WHEN** il clique sur "Autre suggestion"
- **THEN** le système affiche un message "Vous avez reçu le maximum de suggestions. Réessayez après un rafraîchissement."

---

### Requirement: Prediction History

Le système SHALL afficher l'historique des prédictions passées avec date, volume suggéré, et statut de lecture.

#### Scenario: Affichage de l'historique
- **GIVEN** l'utilisateur clique sur "Historique" depuis la PredictionPage
- **WHEN** la page s'affiche
- **THEN** le système récupère l'historique depuis GET /predictions/{user_id}
- **AND** le système affiche une liste chronologique inverse (plus récent d'abord)
- **AND** chaque ligne affiche :
  - Date/heure de la prédiction
  - Couverture et titre du volume suggéré
  - Statut (non lu, en cours, terminé)

#### Scenario: Historique vide
- **GIVEN** aucune prédiction n'a été générée
- **WHEN** la page Historique s'affiche
- **THEN** le système affiche un message "Aucune prédiction pour le moment"

#### Scenario: Navigation depuis l'historique
- **GIVEN** une prédiction est affichée dans l'historique
- **WHEN** l'utilisateur clique sur un volume
- **THEN** le système navigue vers la VolumeDetailPage pour ce volume

---

### Requirement: Volume Integration

Le système SHALL intégrer les suggestions dans le contexte de la collection.

#### Scenario: Accès rapide depuis la collection
- **GIVEN** l'utilisateur est sur CollectionPage
- **WHEN** il clique sur le bouton "Obtenir une suggestion"
- **THEN** le système navigue vers PredictionPage

#### Scenario: Statut de prédiction
- **GIVEN** un volume a été suggéré
- **WHEN** l'utilisateur visualise ce volume dans la collection
- **THEN** le système affiche un badge "Suggéré" ou une indication visuelle
