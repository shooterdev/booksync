# Change: Ajout du Panier d'achats (V2)

## Why
Les utilisateurs veulent gérer une liste de volumes à acheter avec calcul du budget et possibilité de rediriger vers les marchands en ligne (BDFugue, Amazon).

## What Changes
- Ajout d'une page Panier accessible depuis la SideBar
- Gestion des items (volumes + coffrets) avec quantités
- Synchronisation avec les boutiques en ligne via l'API Mangacollec
- Calcul du sous-total et lien d'achat

## Impact
- Affected specs: cart (new)
- Affected code: CartService, CartViewModel, CartPage.qml, SideBar.qml
