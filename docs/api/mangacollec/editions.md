# Éditions — API Mangacollec

Documentation des endpoints liés aux éditions.

---

## Endpoints

| Méthode | Endpoint              | Description              |
|---------|-----------------------|--------------------------|
| GET     | `/v2/editions/{id}`   | Détail d'une édition     |

---

## GET /v2/editions/{id}

Récupère le détail d'une édition spécifique.

### Paramètres

| Paramètre | Type   | Requis | Description              |
|-----------|--------|--------|--------------------------|
| `id`      | string | Oui    | Identifiant de l'édition |

### Réponse

```json
{
    "editions": [
        {
            "id": "345b6656-93a3-43d1-aeb0-6389a269c85d",
            "title": null,
            "series_id": "d1a38f41-2ac9-40cf-aa17-aeac9697e1c8",
            "publisher_id": "4c9547ff-2ef6-439a-80b8-ea705a385b76",
            "parent_edition_id": null,
            "volumes_count": 72,
            "last_volume_number": 72,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 116366
        },
        {
            "id": "ab314a46-2019-4c96-832e-be21b4378f88",
            "title": "Jaquette Fnac",
            "series_id": "d1a38f41-2ac9-40cf-aa17-aeac9697e1c8",
            "publisher_id": "4c9547ff-2ef6-439a-80b8-ea705a385b76",
            "parent_edition_id": "345b6656-93a3-43d1-aeb0-6389a269c85d",
            "volumes_count": 1,
            "last_volume_number": null,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 540
        }
    ],
    "publishers": [
        {
            "id": "4c9547ff-2ef6-439a-80b8-ea705a385b76",
            "title": "Kana",
            "closed": false,
            "editions_count": 595,
            "no_amazon": false
        }
    ],
    "series": [
        {
            "id": "d1a38f41-2ac9-40cf-aa17-aeac9697e1c8",
            "title": "Naruto",
            "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "adult_content": false,
            "editions_count": 7,
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
            "id": "2e0a5bf7-295a-4d54-9a23-8d09d67f1f32",
            "title": null,
            "number": 29,
            "release_date": "2007-05-04",
            "isbn": "9782505000976",
            "asin": "2505000972",
            "edition_id": "345b6656-93a3-43d1-aeb0-6389a269c85d",
            "possessions_count": 40894,
            "not_sold": false,
            "image_url": "https://images-eu.ssl-images-amazon.com/images/I/51GX8g-06hL.jpg"
        }
    ]
}
```

---

## Schéma EditionResponse

Structure de la réponse pour une édition.

```json
{
    "editions": "Edition[]",
    "publishers": "Publisher[]",
    "series": "Series[]",
    "types": "Type[]",
    "volumes": "Volume[]"
}
```

### Champs

| Champ      | Type        | Nullable | Description                              |
|------------|-------------|----------|------------------------------------------|
| editions   | Edition[]   | Non      | Éditions (principale + variantes)        |
| publishers | Publisher[] | Non      | Éditeurs des éditions                    |
| series     | Series[]    | Non      | Séries des éditions                      |
| types      | Type[]      | Non      | Types de séries                          |
| volumes    | Volume[]    | Non      | Volumes des éditions                     |

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

| Champ          | Type    | Nullable | Description                           |
|----------------|---------|----------|---------------------------------------|
| id             | string  | Non      | Identifiant unique de l'éditeur       |
| title          | string  | Non      | Nom de l'éditeur (ex: "Kana")         |
| closed         | boolean | Non      | Éditeur fermé                         |
| editions_count | integer | Non      | Nombre d'éditions publiées            |
| no_amazon      | boolean | Non      | Non disponible sur Amazon             |

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
