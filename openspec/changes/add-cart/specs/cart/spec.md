# Spec: Panier d'achats

## ADDED Requirements

### Requirement: Cart Display

Le système SHALL afficher la liste des volumes et coffrets dans le panier avec couverture, titre, prix, disponibilité, quantité.

#### Scenario: Panier avec items
3 items dans le panier → 3 lignes avec couverture, titre, prix unitaire, quantité, prix total par item.

#### Scenario: Panier vide
Aucun item → message "Votre panier est vide" avec bouton retour vers catalogue.

**Requirement:** The system SHALL display the list of volumes and boxes in the cart with cover, title, price, availability, quantity.

### Requirement: Add to Cart

Le système SHALL permettre d'ajouter un volume au panier depuis la fiche volume.

#### Scenario: Ajout succès
Clic "Ajouter au panier" → item ajouté → badge panier mis à jour (++), toast confirmation.

#### Scenario: Item déjà présent
Item déjà dans panier → augmente quantité au lieu de dupliquer.

**Requirement:** The system SHALL allow adding a volume to the cart from the volume record.

### Requirement: Remove from Cart

Le système SHALL permettre de retirer un item du panier avec confirmation.

#### Scenario: Suppression item
Swipe/bouton suppression → confirmation → item retiré → total recalculé.

#### Scenario: Réduction quantité
Bouton "-" → quantité diminue. Quantité = 0 → item supprimé.

**Requirement:** The system SHALL allow removing an item from the cart with confirmation.

### Requirement: Cart Budget

Le système SHALL afficher le sous-total du panier et le nombre d'items.

#### Scenario: Calcul total
3 items à 7€, 8€, 10€ → "Sous-total: 25,00€ (3 articles)".

#### Scenario: Prix indisponible
Prix N/A pour un item → total affiché comme "N/A - Vérifier disponibilité".

**Requirement:** The system SHALL display the cart subtotal and the number of items.

### Requirement: Purchase Link

Le système SHALL fournir un lien d'achat vers la boutique en ligne pour finaliser l'achat.

#### Scenario: Redirection boutique
Clic "Acheter" → ouverture URL boutique (BDFugue, Amazon) dans navigateur ou navigateur web interne.

#### Scenario: URL personnalisée
Panier synchronisé → URL inclut ID panier pour retrouver items en ligne.

**Requirement:** The system SHALL provide a purchase link to the online store to complete the purchase.
