# Offres Marchands — API Mangacollec

Documentation des endpoints liés aux offres marchands.

---

## Endpoints

| Méthode | Endpoint                   | Description   |
|---------|----------------------------|---------------|
| GET     | `/v1/bdfugue_offer/{isbn}` | Offre BDfugue |
| GET     | `/v1/amazon_offer/{asin}`  | Offre Amazon  |
| GET     | `/v1/img_offer/{isbn}`     | Offre IMG     |

---

## GET /v1/bdfugue_offer/{isbn}

Récupère l'offre BDfugue pour un volume.

### Paramètres

| Paramètre | Type   | Requis | Description    |
|-----------|--------|--------|----------------|
| `isbn`    | string | Oui    | ISBN du volume |

### Réponse

```json
{
    "id": "9782380712957",
    "formatted_price": "7,30 €",
    "availability": "En stock !",
    "merchant": "Expédié et vendu par BDfugue.",
    "store_link": "https://www.bdfugue.com/spy-x-family-tome-8?ref=358"
}
```

---

## GET /v1/amazon_offer/{asin}

Récupère l'offre Amazon pour un volume.

### Paramètres

| Paramètre | Type   | Requis | Description           |
|-----------|--------|--------|-----------------------|
| `asin`    | string | Oui    | ASIN Amazon du volume |

### Réponse

```json
{
    "asin": "2380712956",
    "formatted_price": "7,30 €",
    "availability": "En stock",
    "merchant": "Expédié et vendu par Amazon.",
    "store_link": "https://www.amazon.fr/dp/2380712956?tag=manga-web-21&linkCode=ogi&th=1&psc=1"
}
```

---

## GET /v1/img_offer/{isbn}

Récupère l'offre IMG pour un volume.

> **Note** : Cet endpoint retourne toujours un code `204 No Content`. Il semble non fonctionnel ou désactivé.

### Paramètres

| Paramètre | Type   | Requis | Description    |
|-----------|--------|--------|----------------|
| `isbn`    | string | Oui    | ISBN du volume |

### Réponse

**Code 204** : Aucun contenu retourné.

---

## Schéma BdfugueOffer

Structure d'une offre BDfugue.

```json
{
    "id": "string",
    "formatted_price": "string",
    "availability": "string",
    "merchant": "string",
    "store_link": "string"
}
```

### Champs

| Champ           | Type   | Nullable | Description                              |
|-----------------|--------|----------|------------------------------------------|
| id              | string | Non      | ISBN du volume                           |
| formatted_price | string | Non      | Prix formaté (ex: "7,30 €")              |
| availability    | string | Non      | Disponibilité (ex: "En stock !")         |
| merchant        | string | Non      | Vendeur (ex: "Expédié et vendu par...")  |
| store_link      | string | Non      | URL vers la page produit                 |

---

## Schéma AmazonOffer

Structure d'une offre Amazon.

```json
{
    "asin": "string",
    "formatted_price": "string",
    "availability": "string",
    "merchant": "string",
    "store_link": "string"
}
```

### Champs

| Champ           | Type   | Nullable | Description                              |
|-----------------|--------|----------|------------------------------------------|
| asin            | string | Non      | ASIN Amazon du volume                    |
| formatted_price | string | Non      | Prix formaté (ex: "7,30 €")              |
| availability    | string | Non      | Disponibilité (ex: "En stock")           |
| merchant        | string | Non      | Vendeur (ex: "Expédié et vendu par...")  |
| store_link      | string | Non      | URL vers la page produit Amazon          |

