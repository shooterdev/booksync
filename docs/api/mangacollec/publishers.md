# Éditeurs — API Mangacollec

Documentation des endpoints liés aux éditeurs (publishers).

---

## Endpoints

| Méthode | Endpoint                | Description              |
|---------|-------------------------|--------------------------|
| GET     | `/v2/publishers`        | Liste des éditeurs       |
| GET     | `/v2/publishers/{id}`   | Détail d'un éditeur      |

---

## GET /v2/publishers

Récupère la liste des éditeurs.

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
{
    "publishers": [
        {
            "id": "bdef8c9e-7395-465d-8175-a1b985d4aa92",
            "title": "Pika",
            "closed": false,
            "editions_count": 691,
            "no_amazon": false
        }
    ]
}
```

---

## GET /v2/publishers/{id}

Récupère le détail d'un éditeur spécifique.

### Paramètres

| Paramètre | Type   | Requis | Description              |
|-----------|--------|--------|--------------------------|
| `id`      | string | Oui    | Identifiant de l'éditeur |

### Réponse

```json
{
    "publishers": [
        {
            "id": "bdef8c9e-7395-465d-8175-a1b985d4aa92",
            "title": "Pika",
            "closed": false,
            "editions_count": 691,
            "no_amazon": false
        }
    ],
    "editions": [
        {
            "id": "7f1abf16-9551-4083-b944-fbaf096ef74d",
            "title": null,
            "series_id": "52ad7719-6fce-4339-8c7d-36b16914d59b",
            "publisher_id": "bdef8c9e-7395-465d-8175-a1b985d4aa92",
            "parent_edition_id": null,
            "volumes_count": 1,
            "last_volume_number": 5,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 78
        }
    ],
    "box_editions": [
        {
            "id": "3a10742e-3f8d-48d3-a5c3-0b8a797ff44d",
            "title": "Blue Lock",
            "publisher_id": "bdef8c9e-7395-465d-8175-a1b985d4aa92",
            "boxes_count": 2,
            "adult_content": false,
            "box_follow_editions_count": 1775
        }
    ],
    "series": [
        {
            "id": "52ad7719-6fce-4339-8c7d-36b16914d59b",
            "title": "Kingdom of Quartz",
            "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "adult_content": false,
            "editions_count": 1,
            "tasks_count": 1
        }
    ],
    "types": [
        {
            "id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "title": "Manga",
            "to_display": false
        }
    ],
    "volumes": [
        {
            "id": "05d77f71-c2c5-4ec1-b61f-f20cd66a92db",
            "title": null,
            "number": 1,
            "release_date": "2026-01-21",
            "isbn": "9782811689452",
            "asin": "2811689451",
            "edition_id": "7f1abf16-9551-4083-b944-fbaf096ef74d",
            "possessions_count": 2,
            "not_sold": false,
            "image_url": "https://m.media-amazon.com/images/I/51OkdETM6bL.jpg"
        }
    ],
    "boxes": []
}
```

---

## Schéma PublishersListResponse

Structure de la réponse pour la liste des éditeurs.

```json
{
    "publishers": "Publisher[]"
}
```

### Champs

| Champ      | Type        | Nullable | Description          |
|------------|-------------|----------|----------------------|
| publishers | Publisher[] | Non      | Liste des éditeurs   |

---

## Schéma PublisherDetailResponse

Structure de la réponse pour le détail d'un éditeur.

```json
{
    "publishers": "Publisher[]",
    "editions": "Edition[]",
    "box_editions": "BoxEdition[]",
    "series": "Series[]",
    "types": "Type[]",
    "volumes": "Volume[]",
    "boxes": "Box[]"
}
```

### Champs

| Champ        | Type         | Nullable | Description                    |
|--------------|--------------|----------|--------------------------------|
| publishers   | Publisher[]  | Non      | Éditeur (et éventuels liés)    |
| editions     | Edition[]    | Non      | Éditions de l'éditeur          |
| box_editions | BoxEdition[] | Non      | Éditions de coffrets           |
| series       | Series[]     | Non      | Séries associées               |
| types        | Type[]       | Non      | Types de séries                |
| volumes      | Volume[]     | Non      | Volumes des éditions           |
| boxes        | Box[]        | Non      | Coffrets                       |

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
| title          | string  | Non      | Nom de l'éditeur (ex: "Pika")   |
| closed         | boolean | Non      | Éditeur fermé                   |
| editions_count | integer | Non      | Nombre d'éditions publiées      |
| no_amazon      | boolean | Non      | Non disponible sur Amazon       |

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

## Schéma Series

Structure d'une série.

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

## Schéma Box

Structure d'un coffret.

```json
{
    "id": "string",
    "title": "string",
    "number": "integer",
    "release_date": "date",
    "isbn": "string",
    "asin": "string | null",
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
| asin                  | string  | Oui      | Code ASIN Amazon                           |
| commercial_stop       | boolean | Non      | Coffret arrêté commercialement             |
| box_edition_id        | string  | Non      | Identifiant de l'édition coffret           |
| box_possessions_count | integer | Non      | Nombre d'utilisateurs possédant ce coffret |
| image_url             | string  | Non      | URL de l'image de couverture               |
