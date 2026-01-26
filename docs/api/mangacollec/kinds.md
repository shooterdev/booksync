# Genres — API Mangacollec

Documentation des endpoints liés aux genres (kinds).

---

## Endpoints

| Méthode | Endpoint    | Description      |
|---------|-------------|------------------|
| GET     | `/v2/kinds` | Liste des genres |

---

## GET /v2/kinds

Récupère la liste de tous les genres disponibles.

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
{
    "kinds": [
        {
            "id": "5f2df76f-b8d1-4db6-9e36-506cabdbb1db",
            "title": "Action",
            "series_ids": [
                "ccff3d09-7619-4f3e-834e-7682b92023c0",
                "51287cee-3dcf-4c01-980f-5b0d23b63a0d",
                "2ecd1eb4-bbae-4cdc-b9a7-b9533724df45"
            ]
        }
    ]
}
```

---

## Schéma KindsResponse

Structure de la réponse pour les genres.

```json
{
    "kinds": "Kind[]"
}
```

### Champs

| Champ | Type   | Nullable | Description        |
|-------|--------|----------|--------------------|
| kinds | Kind[] | Non      | Liste des genres   |

---

## Schéma Kind

Structure d'un genre.

```json
{
    "id": "string",
    "title": "string",
    "series_ids": "string[]"
}
```

### Champs

| Champ      | Type     | Nullable | Description                           |
|------------|----------|----------|---------------------------------------|
| id         | string   | Non      | Identifiant unique du genre           |
| title      | string   | Non      | Titre du genre (ex: "Action")         |
| series_ids | string[] | Non      | Identifiants des séries de ce genre   |
