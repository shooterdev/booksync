# Spec: Statistiques de collection

## ADDED Requirements

### Requirement: Publisher Distribution Chart

Le système SHALL afficher un graphique camembert de la répartition des volumes possédés par éditeur.

#### Scenario: Répartition éditeur
50 Kazé, 30 Glénat, 20 Ki-oon → camembert proportionnel avec légende et pourcentages.

#### Scenario: Collection vide
Aucun volume → message "Pas de données pour afficher".

**Requirement:** The system SHALL display a pie chart of the distribution of owned volumes by publisher.

### Requirement: Genre Distribution Chart

Le système SHALL afficher un graphique en barres de la répartition par genre (kinds).

#### Scenario: Répartition genre
40 Action, 25 Romance, 15 Horreur → barres horizontales avec valeurs.

#### Scenario: Genres multiples
Plus de 10 genres → limiter aux 10 premiers + "Autres".

**Requirement:** The system SHALL display a bar chart of the distribution by genre (kinds).

### Requirement: Recent Additions

Le système SHALL afficher les 10 derniers volumes ajoutés à la collection (couverture, titre, date d'ajout).

#### Scenario: Ajout récent
Ajout récent → apparaît en première position de la liste.

#### Scenario: Moins de 10 ajouts
3 ajouts total → affichage des 3 uniquement sans page vide.

**Requirement:** The system SHALL display the 10 most recent volumes added to the collection (cover, title, addition date).

### Requirement: Collection Summary

Le système SHALL afficher un résumé : total volumes, total séries, total éditions, valeur estimée (si disponible).

#### Scenario: Résumé collection
120 volumes, 15 séries → affichage "120 volumes • 15 séries • Valeur ~450€".

#### Scenario: Valeur non disponible
Prix indisponible → afficher "N/A" pour la valeur estimée.

**Requirement:** The system SHALL display a summary: total volumes, total series, total editions, estimated value (if available).
