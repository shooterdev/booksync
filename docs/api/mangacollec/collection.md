# Collection — API Mangacollec

Documentation de l'endpoint principal de collection utilisateur.

---

## Endpoints

| Méthode | Endpoint                  | Description                 |
|---------|---------------------------|-----------------------------|
| GET     | `/v2/users/me/collection` | Collection de l'utilisateur |

> **Voir aussi :**
> - [possessions.md](possessions.md) - Gestion des possessions et suivi d'éditions
> - [reads.md](reads.md) - Gestion des lectures
> - [loans.md](loans.md) - Gestion des prêts et emprunteurs
> - [recommendations.md](recommendations.md) - Recommandations personnalisées

---

## GET /v2/users/me/collection

Récupère la collection complète de l'utilisateur authentifié.

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
{
    "editions": [
        {
            "id": "aa0f2403-cc92-45fb-9ace-438eb242d21c",
            "title": null,
            "series_id": "ba90fa8d-5cec-43c3-ba68-e0189d2ea7de",
            "publisher_id": "e92bb57e-3fa1-4cc0-bd69-44f2f89e502c",
            "parent_edition_id": null,
            "volumes_count": 12,
            "last_volume_number": 12,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 6257
        }
    ],
    "series": [
        {
            "id": "7e1fd99e-bae9-4e67-adc5-0401d45074cd",
            "title": "The Civilization Blaster - Zetsuen no Tempest",
            "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "adult_content": false,
            "editions_count": 1,
            "tasks_count": 3,
            "kinds_ids": [
                "5f2df76f-b8d1-4db6-9e36-506cabdbb1db",
                "eb5eb1b3-9f89-43ab-b0fa-0ef61f10d633",
                "9d6a131c-4b4e-4cf6-a9aa-de78087d672d"
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
            "id": "5f2df76f-b8d1-4db6-9e36-506cabdbb1db",
            "title": "Action"
        }
    ],
    "volumes": [
        {
            "id": "203fb2c8-e54e-4ae0-b3ae-e20c4a9c7632",
            "title": null,
            "number": 4,
            "release_date": "2009-11-04",
            "isbn": "9782203028616",
            "asin": "2203028610",
            "edition_id": "aa0f2403-cc92-45fb-9ace-438eb242d21c",
            "possessions_count": 2801,
            "not_sold": false,
            "image_url": "https://images-eu.ssl-images-amazon.com/images/I/51%2BVM-jQsbL.jpg"
        }
    ],
    "box_editions": [
        {
            "id": "7c983ccc-a2f9-40b3-b566-7ee6a0e23b12",
            "title": "Horimiya",
            "publisher_id": "839c6566-013d-4093-9da2-4bdc71cb3fb0",
            "boxes_count": 1,
            "adult_content": false,
            "box_follow_editions_count": 6265
        }
    ],
    "boxes": [
        {
            "id": "1e9aa5ea-e919-423e-82ae-10bc1bc3e4a8",
            "title": "Edition limitée Tome 9",
            "number": 0,
            "release_date": "2023-04-19",
            "isbn": "9782373498929",
            "asin": "2373498928",
            "commercial_stop": false,
            "box_edition_id": "7c983ccc-a2f9-40b3-b566-7ee6a0e23b12",
            "box_possessions_count": 6035,
            "image_url": "https://m.media-amazon.com/images/I/51nAKZ3IfpL.jpg"
        }
    ],
    "box_volumes": [
        {
            "id": "85772fc7-fa25-424b-b1a9-30d38c26625f",
            "box_id": "1e9aa5ea-e919-423e-82ae-10bc1bc3e4a8",
            "volume_id": "45ef316f-f99c-4adb-8f7f-eacec79c52a5",
            "included": true
        }
    ],
    "follow_editions": [
        {
            "id": "57ae315f-fd22-4661-8937-4f34ad358a13",
            "edition_id": "aa0f2403-cc92-45fb-9ace-438eb242d21c",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "following": true,
            "created_at": "2019-03-07T15:34:48.359Z",
            "updated_at": "2019-03-07T15:34:48.359Z"
        }
    ],
    "possessions": [
        {
            "id": "c917d0ab-b851-4797-86da-4b4910438549",
            "volume_id": "99da0c7e-f316-468c-bdc0-6245caa47fe8",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "created_at": "2019-03-07T15:34:48.337Z"
        }
    ],
    "box_follow_editions": [
        {
            "id": "378db86b-c684-4716-9285-1d826c044821",
            "box_edition_id": "7c983ccc-a2f9-40b3-b566-7ee6a0e23b12",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "following": true,
            "created_at": "2023-06-06T17:33:50.157Z",
            "updated_at": "2023-06-06T17:33:50.157Z"
        }
    ],
    "box_possessions": [
        {
            "id": "0024c8a5-ef20-4851-aca2-419ba8e75c94",
            "box_id": "1e9aa5ea-e919-423e-82ae-10bc1bc3e4a8",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "created_at": "2023-06-06T17:33:50.140Z"
        }
    ],
    "read_editions": [
        {
            "id": "88d63b4d-fead-41a3-828a-b46efd6bb8f8",
            "edition_id": "f3423db9-85f2-4371-878c-aeb7f48733b4",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "reading": true,
            "created_at": "2022-10-06T12:22:04.238Z",
            "updated_at": "2022-10-06T12:22:04.238Z"
        }
    ],
    "reads": [
        {
            "id": "45581bb5-0a92-4ad8-af5e-00215096a5af",
            "volume_id": "ea167cea-f833-4373-ac6b-6da6ad91b909",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "created_at": "2022-06-30T13:10:38.099Z"
        }
    ],
    "borrowers": [
        {
            "id": "124a0720-e5a8-4a10-8658-55f3913007a9",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "title": "sandrableach59",
            "category": "person",
            "created_at": "2023-03-10T15:04:42.189Z"
        },
        {
            "id": "7a2d73ed-e99a-42fd-8e06-ee3a0c158569",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "title": "Steenvoorde",
            "category": "storage",
            "created_at": "2023-03-10T15:10:49.041Z"
        }
    ],
    "loans": [
        {
            "id": "c7629925-06a1-4de4-a3c5-79e74a5e60d3",
            "possession_id": "1619df2a-9993-4947-877d-9c914c903333",
            "borrower_id": "124a0720-e5a8-4a10-8658-55f3913007a9",
            "created_at": "2025-10-04T22:44:45.071Z"
        }
    ]
}
```

---

## Schéma Collection

Structure de la collection utilisateur.

```json
{
    "editions": "Edition[]",
    "series": "Series[]",
    "types": "Type[]",
    "kinds": "Kind[]",
    "volumes": "Volume[]",
    "box_editions": "BoxEdition[]",
    "boxes": "Box[]",
    "box_volumes": "BoxVolume[]",
    "follow_editions": "FollowEdition[]",
    "possessions": "Possession[]",
    "box_follow_editions": "BoxFollowEdition[]",
    "box_possessions": "BoxPossession[]",
    "read_editions": "ReadEdition[]",
    "reads": "Read[]",
    "borrowers": "Borrower[]",
    "loans": "Loan[]"
}
```

### Champs

| Champ               | Type               | Nullable | Description                          |
|---------------------|--------------------|----------|--------------------------------------|
| editions            | Edition[]          | Non      | Éditions suivies par l'utilisateur   |
| series              | Series[]           | Non      | Séries des éditions                  |
| types               | Type[]             | Non      | Types de séries (Manga, Light Novel) |
| kinds               | Kind[]             | Non      | Genres (Action, Romance, etc.)       |
| volumes             | Volume[]           | Non      | Volumes des éditions                 |
| box_editions        | BoxEdition[]       | Non      | Éditions de coffrets                 |
| boxes               | Box[]              | Non      | Coffrets                             |
| box_volumes         | BoxVolume[]        | Non      | Volumes inclus dans les coffrets     |
| follow_editions     | FollowEdition[]    | Non      | Suivi des éditions                   |
| possessions         | Possession[]       | Non      | Volumes possédés                     |
| box_follow_editions | BoxFollowEdition[] | Non      | Suivi des éditions de coffrets       |
| box_possessions     | BoxPossession[]    | Non      | Coffrets possédés                    |
| read_editions       | ReadEdition[]      | Non      | Suivi de lecture par édition         |
| reads               | Read[]             | Non      | Volumes lus                          |
| borrowers           | Borrower[]         | Non      | Emprunteurs/lieux de stockage        |
| loans               | Loan[]             | Non      | Prêts en cours                       |

---

## Schéma Edition

Structure d'une édition (version collection).

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

## Schéma Series

Structure d'une série (version collection).

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

| Champ          | Type     | Nullable | Description                    |
|----------------|----------|----------|--------------------------------|
| id             | string   | Non      | Identifiant unique de la série |
| title          | string   | Non      | Titre de la série              |
| type_id        | string   | Non      | Identifiant du type            |
| adult_content  | boolean  | Non      | Contenu pour adultes           |
| editions_count | integer  | Non      | Nombre d'éditions              |
| tasks_count    | integer  | Non      | Nombre de tâches associées     |
| kinds_ids      | string[] | Non      | Identifiants des genres        |

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

| Champ | Type   | Nullable | Description                    |
|-------|--------|----------|--------------------------------|
| id    | string | Non      | Identifiant unique du genre    |
| title | string | Non      | Titre du genre (ex: "Action")  |

---

## Schéma Volume

Structure d'un volume (version collection).

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

| Champ     | Type    | Nullable | Description                           |
|-----------|---------|----------|---------------------------------------|
| id        | string  | Non      | Identifiant unique de l'association   |
| box_id    | string  | Non      | Identifiant du coffret                |
| volume_id | string  | Non      | Identifiant du volume                 |
| included  | boolean | Non      | Volume inclus dans le coffret         |

---

## Schéma FollowEdition

Structure du suivi d'une édition.

```json
{
    "id": "string",
    "edition_id": "string",
    "user_id": "string",
    "following": "boolean",
    "created_at": "datetime",
    "updated_at": "datetime"
}
```

### Champs

| Champ      | Type     | Nullable | Description                        |
|------------|----------|----------|------------------------------------|
| id         | string   | Non      | Identifiant unique du suivi        |
| edition_id | string   | Non      | Identifiant de l'édition suivie    |
| user_id    | string   | Non      | Identifiant de l'utilisateur       |
| following  | boolean  | Non      | Suivi actif                        |
| created_at | datetime | Non      | Date de création (ISO 8601)        |
| updated_at | datetime | Non      | Date de mise à jour (ISO 8601)     |

---

## Schéma Possession

Structure d'une possession (volume possédé).

```json
{
    "id": "string",
    "volume_id": "string",
    "user_id": "string",
    "created_at": "datetime"
}
```

### Champs

| Champ      | Type     | Nullable | Description                    |
|------------|----------|----------|--------------------------------|
| id         | string   | Non      | Identifiant unique             |
| volume_id  | string   | Non      | Identifiant du volume possédé  |
| user_id    | string   | Non      | Identifiant de l'utilisateur   |
| created_at | datetime | Non      | Date d'ajout (ISO 8601)        |

---

## Schéma BoxFollowEdition

Structure du suivi d'une édition de coffrets.

```json
{
    "id": "string",
    "box_edition_id": "string",
    "user_id": "string",
    "following": "boolean",
    "created_at": "datetime",
    "updated_at": "datetime"
}
```

### Champs

| Champ          | Type     | Nullable | Description                           |
|----------------|----------|----------|---------------------------------------|
| id             | string   | Non      | Identifiant unique du suivi           |
| box_edition_id | string   | Non      | Identifiant de l'édition coffret      |
| user_id        | string   | Non      | Identifiant de l'utilisateur          |
| following      | boolean  | Non      | Suivi actif                           |
| created_at     | datetime | Non      | Date de création (ISO 8601)           |
| updated_at     | datetime | Non      | Date de mise à jour (ISO 8601)        |

---

## Schéma BoxPossession

Structure d'une possession de coffret.

```json
{
    "id": "string",
    "box_id": "string",
    "user_id": "string",
    "created_at": "datetime"
}
```

### Champs

| Champ      | Type     | Nullable | Description                      |
|------------|----------|----------|----------------------------------|
| id         | string   | Non      | Identifiant unique               |
| box_id     | string   | Non      | Identifiant du coffret possédé   |
| user_id    | string   | Non      | Identifiant de l'utilisateur     |
| created_at | datetime | Non      | Date d'ajout (ISO 8601)          |

---

## Schéma ReadEdition

Structure du suivi de lecture par édition.

```json
{
    "id": "string",
    "edition_id": "string",
    "user_id": "string",
    "reading": "boolean",
    "created_at": "datetime",
    "updated_at": "datetime"
}
```

### Champs

| Champ      | Type     | Nullable | Description                        |
|------------|----------|----------|------------------------------------|
| id         | string   | Non      | Identifiant unique                 |
| edition_id | string   | Non      | Identifiant de l'édition           |
| user_id    | string   | Non      | Identifiant de l'utilisateur       |
| reading    | boolean  | Non      | Lecture en cours                   |
| created_at | datetime | Non      | Date de création (ISO 8601)        |
| updated_at | datetime | Non      | Date de mise à jour (ISO 8601)     |

---

## Schéma Read

Structure d'une lecture de volume.

```json
{
    "id": "string",
    "volume_id": "string",
    "user_id": "string",
    "created_at": "datetime"
}
```

### Champs

| Champ      | Type     | Nullable | Description                    |
|------------|----------|----------|--------------------------------|
| id         | string   | Non      | Identifiant unique             |
| volume_id  | string   | Non      | Identifiant du volume lu       |
| user_id    | string   | Non      | Identifiant de l'utilisateur   |
| created_at | datetime | Non      | Date de lecture (ISO 8601)     |

---

## Schéma Borrower

Structure d'un emprunteur ou lieu de stockage.

```json
{
    "id": "string",
    "user_id": "string",
    "title": "string",
    "category": "string",
    "created_at": "datetime"
}
```

### Champs

| Champ      | Type     | Nullable | Description                                   |
|------------|----------|----------|-----------------------------------------------|
| id         | string   | Non      | Identifiant unique                            |
| user_id    | string   | Non      | Identifiant de l'utilisateur                  |
| title      | string   | Non      | Nom de l'emprunteur ou du lieu                |
| category   | string   | Non      | Catégorie : "person" ou "storage"             |
| created_at | datetime | Non      | Date de création (ISO 8601)                   |

---

## Schéma Loan

Structure d'un prêt.

```json
{
    "id": "string",
    "possession_id": "string",
    "borrower_id": "string",
    "created_at": "datetime"
}
```

### Champs

| Champ         | Type     | Nullable | Description                         |
|---------------|----------|----------|-------------------------------------|
| id            | string   | Non      | Identifiant unique du prêt          |
| possession_id | string   | Non      | Identifiant de la possession prêtée |
| borrower_id   | string   | Non      | Identifiant de l'emprunteur         |
| created_at    | datetime | Non      | Date du prêt (ISO 8601)             |
