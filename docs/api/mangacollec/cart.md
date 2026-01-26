# Panier — API Mangacollec

Documentation des endpoints liés au panier d'achats.

---

## Endpoints

| Méthode | Endpoint                               | Description                 |
|---------|----------------------------------------|-----------------------------|
| GET     | `/v1/users/me/cart`                    | Panier de l'utilisateur     |
| POST    | `/v1/users/me/cart`                    | Ajouter un volume au panier |
| DELETE  | `/v1/users/me/cart/volume/{id_volume}` | Supprimer un volume         |

---

## GET /v1/users/me/cart

Récupère le panier de l'utilisateur authentifié.

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
{
    "id": "...",
    "store": "...",
    "store_cart_id": null,
    "store_cart_sub_total": "121,75 €",
    "store_cart_purchase_url": "...",
    "store_cart_active": true,
    "items": [
        {
            "id": "...",
            "cart_id": "...",
            "volume_id": "...",
            "quantity": 1,
            "created_at": "2026-01-21T15:05:50.889Z",
            "store_cart_item_id": null,
            "store_cart_item_price": "6,95 €",
            "store_cart_item_link": "...",
            "store_cart_item_availability": "En stock !",
            "volume": {
                "id": "...",
                "title": null,
                "number": 11,
                "release_date": "2026-01-23",
                "image_url": "...",
                "isbn": "9782385038151",
                "asin": "2385038153",
                "edition_id": "...",
                "possessions_count": 103,
                "not_sold": false,
                "edition": {
                    "id": "...",
                    "title": null,
                    "series_id": "...",
                    "publisher_id": "...",
                    "parent_edition_id": null,
                    "volumes_count": 12,
                    "last_volume_number": null,
                    "commercial_stop": false,
                    "not_finished": false,
                    "follow_editions_count": 5765,
                    "series": {
                        "id": "...",
                        "title": "Failure Frame",
                        "type_id": "...",
                        "adult_content": false,
                        "editions_count": 1,
                        "tasks_count": 4,
                        "type": {
                            "id": "...",
                            "title": "Manga",
                            "to_display": false
                        }
                    }
                }
            }
        }
    ],
    "box_items": []
}
```

---

## POST /v1/users/me/cart

Ajoute un volume au panier de l'utilisateur.

### Corps de la requête

```json
[
    {
        "volume_id": "5732a254-c687-4868-b095-2e94e1144ae2"
    }
]
```

### Paramètres du corps

| Paramètre | Type   | Requis | Description                     |
|-----------|--------|--------|---------------------------------|
| volume_id | string | Oui    | Identifiant du volume à ajouter |

### Réponse

Retourne le contenu du panier mis à jour (voir schéma Cart).

```json
{
    "id": "...",
    "store": "...",
    "store_cart_id": null,
    "store_cart_sub_total": "121,75 €",
    "store_cart_purchase_url": "...",
    "store_cart_active": true,
    "items": [],
    "box_items": []
}
```

> Structure complète : voir schéma Cart ci-dessous.

---

## DELETE /v1/users/me/cart/volume/{id_volume}

Supprime un volume du panier de l'utilisateur.

### Paramètres

| Paramètre | Type   | Requis | Description                       |
|-----------|--------|--------|-----------------------------------|
| id_volume | string | Oui    | Identifiant du volume à supprimer |

### Réponse

**Succès (200)**

Retourne le contenu du panier mis à jour (voir schéma Cart).

**Erreur (404)**

```json
{
    "errors": ["Couldn't find this record"]
}
```

---

## Schéma Cart

Structure du panier utilisateur.

```json
{
    "id": "string",
    "store": "string",
    "store_cart_id": "string | null",
    "store_cart_sub_total": "string",
    "store_cart_purchase_url": "string",
    "store_cart_active": "boolean",
    "items": "CartItem[]",
    "box_items": "BoxItem[]"
}
```

### Champs

| Champ                    | Type         | Nullable | Description                                |
|--------------------------|--------------|----------|--------------------------------------------|
| id                       | string       | Non      | Identifiant unique du panier               |
| store                    | string       | Non      | Identifiant ou nom du magasin              |
| store_cart_id            | string       | Oui      | Identifiant du panier côté magasin         |
| store_cart_sub_total     | string       | Non      | Sous-total formaté (ex: "121,75 €")        |
| store_cart_purchase_url  | string       | Non      | URL pour finaliser l'achat                 |
| store_cart_active        | boolean      | Non      | Indique si le panier est actif             |
| items                    | CartItem[]   | Non      | Liste des articles du panier               |
| box_items                | BoxItem[]    | Non      | Liste des coffrets du panier               |

---

## Schéma CartItem

Structure d'un élément du panier.

```json
{
    "id": "string",
    "cart_id": "string",
    "volume_id": "string",
    "quantity": "integer",
    "created_at": "datetime",
    "store_cart_item_id": "string | null",
    "store_cart_item_price": "string",
    "store_cart_item_link": "string",
    "store_cart_item_availability": "string",
    "volume": "Volume"
}
```

### Champs

| Champ                        | Type     | Nullable | Description                            |
|------------------------------|----------|----------|----------------------------------------|
| id                           | string   | Non      | Identifiant unique de l'article        |
| cart_id                      | string   | Non      | Identifiant du panier parent           |
| volume_id                    | string   | Non      | Identifiant du volume associé          |
| quantity                     | integer  | Non      | Quantité de l'article                  |
| created_at                   | datetime | Non      | Date d'ajout au panier (ISO 8601)      |
| store_cart_item_id           | string   | Oui      | Identifiant de l'article côté magasin  |
| store_cart_item_price        | string   | Non      | Prix formaté (ex: "6,95 €")            |
| store_cart_item_link         | string   | Non      | Lien vers l'article sur le magasin     |
| store_cart_item_availability | string   | Non      | Disponibilité (ex: "En stock !")       |
| volume                       | Volume   | Non      | Détails du volume (voir schéma Volume) |

---

## Schéma Volume

Structure d'un volume dans le panier.

```json
{
    "id": "string",
    "title": "string | null",
    "number": "integer",
    "release_date": "date",
    "image_url": "string",
    "isbn": "string",
    "asin": "string",
    "edition_id": "string",
    "possessions_count": "integer",
    "not_sold": "boolean",
    "edition": "Edition"
}
```

### Champs

| Champ             | Type    | Nullable | Description                               |
|-------------------|---------|----------|-------------------------------------------|
| id                | string  | Non      | Identifiant unique du volume              |
| title             | string  | Oui      | Titre du volume (si différent de série)   |
| number            | integer | Non      | Numéro du volume                          |
| release_date      | date    | Non      | Date de sortie (YYYY-MM-DD)               |
| image_url         | string  | Non      | URL de l'image de couverture              |
| isbn              | string  | Non      | Code ISBN-13                              |
| asin              | string  | Non      | Code ASIN Amazon                          |
| edition_id        | string  | Non      | Identifiant de l'édition                  |
| possessions_count | integer | Non      | Nombre d'utilisateurs possédant ce volume |
| not_sold          | boolean | Non      | Indique si le volume n'est plus vendu     |
| edition           | Edition | Non      | Détails de l'édition (voir schéma)        |

---

## Schéma Edition

Structure d'une édition.

```json
{
    "id": "string",
    "title": "string | null",
    "series_id": "string",
    "publisher_id": "string",
    "parent_edition_id": "string | null",
    "volumes_count": "integer",
    "last_volume_number": "integer | null",
    "commercial_stop": "boolean",
    "not_finished": "boolean",
    "follow_editions_count": "integer",
    "series": "Series"
}
```

### Champs

| Champ                 | Type    | Nullable | Description                                |
|-----------------------|---------|----------|--------------------------------------------|
| id                    | string  | Non      | Identifiant unique de l'édition            |
| title                 | string  | Oui      | Titre de l'édition (si différent de série) |
| series_id             | string  | Non      | Identifiant de la série                    |
| publisher_id          | string  | Non      | Identifiant de l'éditeur                   |
| parent_edition_id     | string  | Oui      | Identifiant de l'édition parente           |
| volumes_count         | integer | Non      | Nombre total de volumes                    |
| last_volume_number    | integer | Oui      | Numéro du dernier volume                   |
| commercial_stop       | boolean | Non      | Édition arrêtée commercialement            |
| not_finished          | boolean | Non      | Série non terminée                         |
| follow_editions_count | integer | Non      | Nombre d'utilisateurs suivant l'édition    |
| series                | Series  | Non      | Détails de la série (voir schéma)          |

---

## Schéma Series

Structure d'une série.

```json
{
    "id": "string",
    "title": "string",
    "type_id": "string",
    "adult_content": "boolean",
    "editions_count": "integer",
    "tasks_count": "integer",
    "type": "Type"
}
```

### Champs

| Champ          | Type    | Nullable | Description                          |
|----------------|---------|----------|--------------------------------------|
| id             | string  | Non      | Identifiant unique de la série       |
| title          | string  | Non      | Titre de la série                    |
| type_id        | string  | Non      | Identifiant du type                  |
| adult_content  | boolean | Non      | Contenu pour adultes                 |
| editions_count | integer | Non      | Nombre d'éditions                    |
| tasks_count    | integer | Non      | Nombre de tâches associées           |
| type           | Type    | Non      | Détails du type (voir schéma)        |

---

## Schéma Type

Structure d'un type de série.

```json
{
    "id": "string",
    "title": "string",
    "to_display": "boolean"
}
```

### Champs

| Champ      | Type    | Nullable | Description                          |
|------------|---------|----------|--------------------------------------|
| id         | string  | Non      | Identifiant unique du type           |
| title      | string  | Non      | Titre du type (ex: "Manga")          |
| to_display | boolean | Non      | Indique si le type doit être affiché |
