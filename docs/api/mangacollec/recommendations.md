# Recommandations — API Mangacollec

Documentation de l'endpoint de recommandations personnalisées.

---

## Endpoints

| Méthode | Endpoint                      | Description    |
|---------|-------------------------------|----------------|
| GET     | `/v1/users/me/recommendation` | Recommandations|

---

## GET /v1/users/me/recommendation

Récupère les recommandations personnalisées pour l'utilisateur.

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
[
    {
        "id": "4b559ff5-c7ff-437a-a879-6937d46cce78",
        "title": null,
        "number": 0,
        "release_date": "2026-01-08",
        "image_url": "https://m.media-amazon.com/images/I/51kfT8RjA-L.jpg",
        "isbn": "9791032724613",
        "asin": "B0FRPS7G7T",
        "edition_id": "5fa134d9-2426-40db-83bf-47efb1ae62ba",
        "possessions_count": 370,
        "not_sold": false,
        "edition": {
            "id": "5fa134d9-2426-40db-83bf-47efb1ae62ba",
            "title": null,
            "series_id": "cd88ab99-cd31-459c-a6c3-7a8ebed9c8ee",
            "publisher_id": "74de7d1e-f2e9-44bb-8a5a-1fe84258c7bf",
            "parent_edition_id": null,
            "volumes_count": 2,
            "last_volume_number": null,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 778,
            "series": {
                "id": "cd88ab99-cd31-459c-a6c3-7a8ebed9c8ee",
                "title": "Mechanical Buddy Universe",
                "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
                "adult_content": false,
                "editions_count": 1,
                "tasks_count": 1,
                "tasks": [
                    {
                        "id": "c9c7b475-a622-421d-a8d9-7f8dc7fd4f71",
                        "job_id": "dc7b6062-6aae-49ee-87a2-95d47ab52600",
                        "series_id": "cd88ab99-cd31-459c-a6c3-7a8ebed9c8ee",
                        "author_id": "4e15771b-0d63-4680-b22e-d96386d449f9",
                        "author": {
                            "id": "4e15771b-0d63-4680-b22e-d96386d449f9",
                            "name": "Katô",
                            "first_name": "Takuji",
                            "tasks_count": 2,
                            "tasks": [
                                {
                                    "id": "66158cc3-d9d5-4067-be54-a43dd472fb78",
                                    "job_id": "37fb2477-ec0d-4ffc-9d3b-4189c2d01629",
                                    "series_id": "082db6aa-63bf-473f-a391-dc432aed859e",
                                    "author_id": "4e15771b-0d63-4680-b22e-d96386d449f9",
                                    "series": {
                                        "id": "082db6aa-63bf-473f-a391-dc432aed859e",
                                        "title": "Apeiron",
                                        "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
                                        "adult_content": false,
                                        "editions_count": 1,
                                        "tasks_count": 2
                                    },
                                    "job": {
                                        "id": "37fb2477-ec0d-4ffc-9d3b-4189c2d01629",
                                        "title": "Dessin"
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        }
    }
]
```

---

## Schéma RecommendationVolume

Structure d'un volume recommandé (avec édition et série imbriquées).

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
    "edition": "RecommendationEdition"
}
```

### Champs

| Champ             | Type                  | Nullable | Description                               |
|-------------------|-----------------------|----------|-------------------------------------------|
| id                | string                | Non      | Identifiant unique du volume              |
| title             | string                | Oui      | Titre du volume (si différent de série)   |
| number            | integer               | Non      | Numéro du volume                          |
| release_date      | date                  | Non      | Date de sortie (YYYY-MM-DD)               |
| image_url         | string                | Non      | URL de l'image de couverture              |
| isbn              | string                | Non      | Code ISBN-13                              |
| asin              | string                | Non      | Code ASIN Amazon                          |
| edition_id        | string                | Non      | Identifiant de l'édition                  |
| possessions_count | integer               | Non      | Nombre d'utilisateurs possédant ce volume |
| not_sold          | boolean               | Non      | Indique si le volume n'est plus vendu     |
| edition           | RecommendationEdition | Non      | Détails de l'édition (voir schéma)        |

---

## Schéma RecommendationEdition

Structure d'une édition dans les recommandations (avec série imbriquée).

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
    "series": "RecommendationSeries"
}
```

### Champs

| Champ                 | Type                 | Nullable | Description                                |
|-----------------------|----------------------|----------|--------------------------------------------|
| id                    | string               | Non      | Identifiant unique de l'édition            |
| title                 | string               | Oui      | Titre de l'édition (si différent de série) |
| series_id             | string               | Non      | Identifiant de la série                    |
| publisher_id          | string               | Non      | Identifiant de l'éditeur                   |
| parent_edition_id     | string               | Oui      | Identifiant de l'édition parente           |
| volumes_count         | integer              | Non      | Nombre total de volumes                    |
| last_volume_number    | integer              | Oui      | Numéro du dernier volume                   |
| commercial_stop       | boolean              | Non      | Édition arrêtée commercialement            |
| not_finished          | boolean              | Non      | Série non terminée                         |
| follow_editions_count | integer              | Non      | Nombre d'utilisateurs suivant l'édition    |
| series                | RecommendationSeries | Non      | Détails de la série (voir schéma)          |

---

## Schéma RecommendationSeries

Structure d'une série dans les recommandations (avec tâches imbriquées).

```json
{
    "id": "string",
    "title": "string",
    "type_id": "string",
    "adult_content": "boolean",
    "editions_count": "integer",
    "tasks_count": "integer",
    "tasks": "Task[]"
}
```

### Champs

| Champ          | Type    | Nullable | Description                    |
|----------------|---------|----------|--------------------------------|
| id             | string  | Non      | Identifiant unique de la série |
| title          | string  | Non      | Titre de la série              |
| type_id        | string  | Non      | Identifiant du type            |
| adult_content  | boolean | Non      | Contenu pour adultes           |
| editions_count | integer | Non      | Nombre d'éditions              |
| tasks_count    | integer | Non      | Nombre de tâches associées     |
| tasks          | Task[]  | Non      | Liste des tâches (auteurs)     |

---

## Schéma Task

Structure d'une tâche (relation auteur-série-métier).

```json
{
    "id": "string",
    "job_id": "string",
    "series_id": "string",
    "author_id": "string",
    "author": "Author",
    "job": "Job",
    "series": "SeriesLight"
}
```

### Champs

| Champ     | Type        | Nullable | Description                           |
|-----------|-------------|----------|---------------------------------------|
| id        | string      | Non      | Identifiant unique de la tâche        |
| job_id    | string      | Non      | Identifiant du métier                 |
| series_id | string      | Non      | Identifiant de la série               |
| author_id | string      | Non      | Identifiant de l'auteur               |
| author    | Author      | Oui      | Détails de l'auteur (si inclus)       |
| job       | Job         | Oui      | Détails du métier (si inclus)         |
| series    | SeriesLight | Oui      | Détails de la série (si inclus)       |

---

## Schéma Author

Structure d'un auteur.

```json
{
    "id": "string",
    "name": "string",
    "first_name": "string",
    "tasks_count": "integer",
    "tasks": "Task[]"
}
```

### Champs

| Champ       | Type    | Nullable | Description                        |
|-------------|---------|----------|------------------------------------|
| id          | string  | Non      | Identifiant unique de l'auteur     |
| name        | string  | Non      | Nom de famille                     |
| first_name  | string  | Non      | Prénom                             |
| tasks_count | integer | Non      | Nombre de tâches (séries)          |
| tasks       | Task[]  | Oui      | Liste des tâches (autres œuvres)   |

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

| Champ | Type   | Nullable | Description                          |
|-------|--------|----------|--------------------------------------|
| id    | string | Non      | Identifiant unique du métier         |
| title | string | Non      | Titre du métier (ex: "Dessin")       |

---

## Schéma SeriesLight

Structure d'une série (version légère pour les tâches).

```json
{
    "id": "string",
    "title": "string",
    "type_id": "string",
    "adult_content": "boolean",
    "editions_count": "integer",
    "tasks_count": "integer"
}
```

### Champs

| Champ          | Type    | Nullable | Description                    |
|----------------|---------|----------|--------------------------------|
| id             | string  | Non      | Identifiant unique de la série |
| title          | string  | Non      | Titre de la série              |
| type_id        | string  | Non      | Identifiant du type            |
| adult_content  | boolean | Non      | Contenu pour adultes           |
| editions_count | integer | Non      | Nombre d'éditions              |
| tasks_count    | integer | Non      | Nombre de tâches associées     |
