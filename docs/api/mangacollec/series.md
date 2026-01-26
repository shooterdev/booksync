# Séries — API Mangacollec

Documentation des endpoints liés aux séries.

---

## Endpoints

| Méthode | Endpoint            | Description              |
|---------|---------------------|--------------------------|
| GET     | `/v2/series`        | Recherche de séries      |
| GET     | `/v2/series/{id}`   | Détail d'une série       |

---

## GET /v2/series

Recherche et liste des séries.

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
{
    "series": [
        {
            "id": "a02cf154-af6c-4f08-9a7a-32f7bc229ac8",
            "title": "One Piece",
            "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "adult_content": false,
            "editions_count": 5,
            "tasks_count": 1
        }
    ],
    "types": [
        {
            "id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "title": "Manga",
            "to_display": false
        }
    ]
}
```

---

## GET /v2/series/{id}

Récupère le détail d'une série spécifique.

### Paramètres

| Paramètre | Type   | Requis | Description             |
|-----------|--------|--------|-------------------------|
| `id`      | string | Oui    | Identifiant de la série |

### Réponse

```json
{
    "series": [
        {
            "id": "56057d0f-86e9-4e55-bfba-3b284af71855",
            "title": "Oshi no Ko",
            "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "adult_content": false,
            "editions_count": 4,
            "tasks_count": 2,
            "kinds_ids": [
                "acd6bec9-507d-46ab-a1de-f18f8b8ca83c",
                "801ba7fd-e690-4272-837d-e52f155b72e5",
                "fc8b3d02-6e73-4354-b0e1-7d361b6c64d5",
                "83558195-a5ee-4acb-a392-9da6f625ad7f"
            ]
        }
    ],
    "types": [
        {
            "id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "title": "Manga",
            "to_display": false
        }
    ],
    "kinds": [
        {
            "id": "acd6bec9-507d-46ab-a1de-f18f8b8ca83c",
            "title": "Drame"
        }
    ],
    "tasks": [
        {
            "id": "63887b4f-a5e3-4173-80b6-efaf7c214662",
            "job_id": "37fb2477-ec0d-4ffc-9d3b-4189c2d01629",
            "series_id": "56057d0f-86e9-4e55-bfba-3b284af71855",
            "author_id": "1d6a6957-ab43-4134-8114-81036ea65efc"
        }
    ],
    "jobs": [
        {
            "id": "37fb2477-ec0d-4ffc-9d3b-4189c2d01629",
            "title": "Dessin"
        }
    ],
    "authors": [
        {
            "id": "4bb4f319-e123-4a16-ba79-9574f6fd628b",
            "name": "Akasaka",
            "first_name": "Aka",
            "tasks_count": 4
        }
    ],
    "editions": [
        {
            "id": "6b5ad590-e72f-4fb8-af6d-b6436af88f0e",
            "title": "Edition limitée",
            "series_id": "56057d0f-86e9-4e55-bfba-3b284af71855",
            "publisher_id": "2f064f84-a653-48c1-b1f6-27a694bb7ec6",
            "parent_edition_id": "9dd39743-aa3c-4815-a2c2-93e3b391c5d5",
            "volumes_count": 1,
            "last_volume_number": null,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 5191
        }
    ],
    "publishers": [
        {
            "id": "2f064f84-a653-48c1-b1f6-27a694bb7ec6",
            "title": "Kurokawa",
            "closed": false,
            "editions_count": 300,
            "no_amazon": false
        }
    ],
    "volumes": [
        {
            "id": "1141ff10-9ff6-4d5a-bbd9-2072326499ec",
            "title": null,
            "number": 16,
            "release_date": "2025-12-04",
            "isbn": "9791042018962",
            "asin": "B0FC1P1SSJ",
            "edition_id": "9dd39743-aa3c-4815-a2c2-93e3b391c5d5",
            "possessions_count": 110,
            "not_sold": false,
            "image_url": "https://m.media-amazon.com/images/I/51yf-XlSq4L.jpg"
        }
    ],
    "box_editions": [
        {
            "id": "5c480ec5-60c3-4320-a78e-149d21d09f8a",
            "title": "Oshi no Ko",
            "publisher_id": "2f064f84-a653-48c1-b1f6-27a694bb7ec6",
            "boxes_count": 1,
            "adult_content": false,
            "box_follow_editions_count": 726
        }
    ],
    "boxes": [
        {
            "id": "574a5170-7125-4e88-86fc-492e4b96a0ad",
            "title": "Edition Collector Tome 16",
            "number": 0,
            "release_date": "2025-12-04",
            "isbn": "9791042020842",
            "asin": "B0FC1R7LJ5",
            "commercial_stop": false,
            "box_edition_id": "5c480ec5-60c3-4320-a78e-149d21d09f8a",
            "box_possessions_count": 56,
            "image_url": "https://m.media-amazon.com/images/I/71Rj7jZKNwL.jpg"
        }
    ],
    "box_volumes": [
        {
            "id": "fe7036c1-0daf-4581-83c9-b0ea5c1c3f54",
            "box_id": "f568fd16-2189-4ab1-a16c-3b81c44c83ed",
            "volume_id": "13198072-6618-4d1f-aa03-dc2c8d7fff07",
            "included": true
        }
    ]
}
```

---

## Schéma SeriesListResponse

Structure de la réponse pour la liste des séries.

```json
{
    "series": "Series[]",
    "types": "Type[]"
}
```

### Champs

| Champ  | Type     | Nullable | Description        |
|--------|----------|----------|--------------------|
| series | Series[] | Non      | Liste des séries   |
| types  | Type[]   | Non      | Types de séries    |

---

## Schéma SeriesDetailResponse

Structure de la réponse pour le détail d'une série.

```json
{
    "series": "Series[]",
    "types": "Type[]",
    "kinds": "Kind[]",
    "tasks": "Task[]",
    "jobs": "Job[]",
    "authors": "Author[]",
    "editions": "Edition[]",
    "publishers": "Publisher[]",
    "volumes": "Volume[]",
    "box_editions": "BoxEdition[]",
    "boxes": "Box[]",
    "box_volumes": "BoxVolume[]"
}
```

### Champs

| Champ        | Type         | Nullable | Description                    |
|--------------|--------------|----------|--------------------------------|
| series       | Series[]     | Non      | Série (avec kinds_ids)         |
| types        | Type[]       | Non      | Types de séries                |
| kinds        | Kind[]       | Non      | Genres de la série             |
| tasks        | Task[]       | Non      | Tâches (auteurs/métiers)       |
| jobs         | Job[]        | Non      | Métiers                        |
| authors      | Author[]     | Non      | Auteurs                        |
| editions     | Edition[]    | Non      | Éditions de la série           |
| publishers   | Publisher[]  | Non      | Éditeurs                       |
| volumes      | Volume[]     | Non      | Volumes des éditions           |
| box_editions | BoxEdition[] | Non      | Éditions de coffrets           |
| boxes        | Box[]        | Non      | Coffrets                       |
| box_volumes  | BoxVolume[]  | Non      | Volumes inclus dans coffrets   |

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
    "kinds_ids": "string[]"
}
```

### Champs

| Champ          | Type     | Nullable | Description                           |
|----------------|----------|----------|---------------------------------------|
| id             | string   | Non      | Identifiant unique de la série        |
| title          | string   | Non      | Titre de la série                     |
| type_id        | string   | Non      | Identifiant du type                   |
| adult_content  | boolean  | Non      | Contenu pour adultes                  |
| editions_count | integer  | Non      | Nombre d'éditions                     |
| tasks_count    | integer  | Non      | Nombre de tâches associées            |
| kinds_ids      | string[] | Oui      | Identifiants des genres (détail only) |

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

---

## Schéma Kind

Structure d'un genre.

```json
{
    "id": "string",
    "title": "string"
}
```

### Champs

| Champ | Type   | Nullable | Description                   |
|-------|--------|----------|-------------------------------|
| id    | string | Non      | Identifiant unique du genre   |
| title | string | Non      | Titre du genre (ex: "Drame")  |

---

## Schéma Task

Structure d'une tâche (relation auteur-série-métier).

```json
{
    "id": "string",
    "job_id": "string",
    "series_id": "string",
    "author_id": "string"
}
```

### Champs

| Champ     | Type   | Nullable | Description                    |
|-----------|--------|----------|--------------------------------|
| id        | string | Non      | Identifiant unique de la tâche |
| job_id    | string | Non      | Identifiant du métier          |
| series_id | string | Non      | Identifiant de la série        |
| author_id | string | Non      | Identifiant de l'auteur        |

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

| Champ | Type   | Nullable | Description                    |
|-------|--------|----------|--------------------------------|
| id    | string | Non      | Identifiant unique du métier   |
| title | string | Non      | Titre du métier (ex: "Dessin") |

---

## Schéma Author

Structure d'un auteur.

```json
{
    "id": "string",
    "name": "string",
    "first_name": "string",
    "tasks_count": "integer"
}
```

### Champs

| Champ       | Type    | Nullable | Description                    |
|-------------|---------|----------|--------------------------------|
| id          | string  | Non      | Identifiant unique de l'auteur |
| name        | string  | Non      | Nom de famille                 |
| first_name  | string  | Non      | Prénom                         |
| tasks_count | integer | Non      | Nombre de tâches (séries)      |

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
    "follow_editions_count": "integer"
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

---

## Schéma Publisher

Structure d'un éditeur.

```json
{
    "id": "string",
    "title": "string",
    "closed": "boolean",
    "editions_count": "integer",
    "no_amazon": "boolean"
}
```

### Champs

| Champ          | Type    | Nullable | Description                     |
|----------------|---------|----------|---------------------------------|
| id             | string  | Non      | Identifiant unique de l'éditeur |
| title          | string  | Non      | Nom de l'éditeur                |
| closed         | boolean | Non      | Éditeur fermé                   |
| editions_count | integer | Non      | Nombre d'éditions publiées      |
| no_amazon      | boolean | Non      | Non disponible sur Amazon       |

---

## Schéma Volume

Structure d'un volume.

```json
{
    "id": "string",
    "title": "string | null",
    "number": "integer",
    "release_date": "date",
    "isbn": "string",
    "asin": "string",
    "edition_id": "string",
    "possessions_count": "integer",
    "not_sold": "boolean",
    "image_url": "string"
}
```

### Champs

| Champ             | Type    | Nullable | Description                               |
|-------------------|---------|----------|-------------------------------------------|
| id                | string  | Non      | Identifiant unique du volume              |
| title             | string  | Oui      | Titre du volume (si différent de série)   |
| number            | integer | Non      | Numéro du volume                          |
| release_date      | date    | Non      | Date de sortie (YYYY-MM-DD)               |
| isbn              | string  | Non      | Code ISBN-13                              |
| asin              | string  | Non      | Code ASIN Amazon                          |
| edition_id        | string  | Non      | Identifiant de l'édition                  |
| possessions_count | integer | Non      | Nombre d'utilisateurs possédant ce volume |
| not_sold          | boolean | Non      | Indique si le volume n'est plus vendu     |
| image_url         | string  | Non      | URL de l'image de couverture              |

---

## Schéma BoxEdition

Structure d'une édition de coffrets.

```json
{
    "id": "string",
    "title": "string",
    "publisher_id": "string",
    "boxes_count": "integer",
    "adult_content": "boolean",
    "box_follow_editions_count": "integer"
}
```

### Champs

| Champ                     | Type    | Nullable | Description                             |
|---------------------------|---------|----------|-----------------------------------------|
| id                        | string  | Non      | Identifiant unique de l'édition coffret |
| title                     | string  | Non      | Titre de l'édition coffret              |
| publisher_id              | string  | Non      | Identifiant de l'éditeur                |
| boxes_count               | integer | Non      | Nombre de coffrets dans l'édition       |
| adult_content             | boolean | Non      | Contenu pour adultes                    |
| box_follow_editions_count | integer | Non      | Nombre d'utilisateurs suivant l'édition |

---

## Schéma Box

Structure d'un coffret.

```json
{
    "id": "string",
    "title": "string",
    "number": "integer",
    "release_date": "date",
    "isbn": "string",
    "asin": "string",
    "commercial_stop": "boolean",
    "box_edition_id": "string",
    "box_possessions_count": "integer",
    "image_url": "string"
}
```

### Champs

| Champ                 | Type    | Nullable | Description                                |
|-----------------------|---------|----------|--------------------------------------------|
| id                    | string  | Non      | Identifiant unique du coffret              |
| title                 | string  | Non      | Titre du coffret                           |
| number                | integer | Non      | Numéro du coffret                          |
| release_date          | date    | Non      | Date de sortie (YYYY-MM-DD)                |
| isbn                  | string  | Non      | Code ISBN-13                               |
| asin                  | string  | Non      | Code ASIN Amazon                           |
| commercial_stop       | boolean | Non      | Coffret arrêté commercialement             |
| box_edition_id        | string  | Non      | Identifiant de l'édition coffret           |
| box_possessions_count | integer | Non      | Nombre d'utilisateurs possédant ce coffret |
| image_url             | string  | Non      | URL de l'image de couverture               |

---

## Schéma BoxVolume

Structure d'un volume inclus dans un coffret.

```json
{
    "id": "string",
    "box_id": "string",
    "volume_id": "string",
    "included": "boolean"
}
```

### Champs

| Champ     | Type    | Nullable | Description                         |
|-----------|---------|----------|-------------------------------------|
| id        | string  | Non      | Identifiant unique de l'association |
| box_id    | string  | Non      | Identifiant du coffret              |
| volume_id | string  | Non      | Identifiant du volume               |
| included  | boolean | Non      | Volume inclus dans le coffret       |
