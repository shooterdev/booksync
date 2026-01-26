# Jobs (Métiers) — API Mangacollec

Documentation des endpoints liés aux métiers (scénariste, dessinateur, etc.).

---

## Endpoints

| Méthode | Endpoint   | Description       |
|---------|------------|-------------------|
| GET     | `/v1/jobs` | Liste des métiers |

---

## GET /v1/jobs

Récupère la liste de tous les métiers disponibles.

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
[
    {
        "id": "dc7b6062-6aae-49ee-87a2-95d47ab52600",
        "title": "Auteur"
    }
]
```

---

## Schéma Job

Structure d'un métier.

```json
{
    "id": "string",
    "title": "string"
}
```

### Champs

| Champ | Type   | Nullable | Description                             |
|-------|--------|----------|-----------------------------------------|
| id    | string | Non      | Identifiant unique du métier            |
| title | string | Non      | Titre du métier (ex: "Dessin")          |
