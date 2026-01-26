# Planning — API Mangacollec

Documentation des endpoints liés au planning des sorties.

---

## Endpoints

| Méthode | Endpoint                                | Description                   |
|---------|-----------------------------------------|-------------------------------|
| GET     | `/v2/planning`                          | Planning des sorties          |
| GET     | `/v2/users/me/ad_native_planning_perso` | Planning personnalisé natif   |

---

## GET /v2/planning

Récupère le planning des sorties pour un mois donné.

### Paramètres

| Paramètre | Type   | Requis | Description              |
|-----------|--------|--------|--------------------------|
| `month`   | string | Non    | Mois au format `YYYY-MM` |

### Réponse

```json
{
    "volumes": [
        {
            "id": "8c52b8f1-2724-4591-9768-03dd903e30d7",
            "title": null,
            "number": 0,
            "release_date": "2022-09-01",
            "isbn": " 9781963040111",
            "asin": null,
            "edition_id": "ed79d637-0e58-420c-a5c1-37a89fd68a67",
            "possessions_count": 31,
            "not_sold": false,
            "image_url": "https://api.mangacollec.com/rails/active_storage/representations/redirect/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaEpJaWt6T1RZeU9UQTJPUzB5T0RSa0xUUTVPVFF0WVRVMFpDMHhOMkZtWVRrME16VTNObVlHT2daRlZBPT0iLCJleHAiOm51bGwsInB1ciI6ImJsb2JfaWQifX0=--66f90491b44f7200bd8b66254c7a4d770795da82/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaDdCem9MWm05eWJXRjBTU0lJYW5CbkJqb0dSVlE2RkhKbGMybDZaVjkwYjE5c2FXMXBkRnNIYVFMMEFXa0M5QUU9IiwiZXhwIjpudWxsLCJwdXIiOiJ2YXJpYXRpb24ifX0=--f4740ce9863997714730d7d28982d4fbc8d883df/8c52b8f1-2724-4591-9768-03dd903e30d7.jpg"
        }
    ],
    "editions": [
        {
            "id": "e64bd23e-ab98-4f9e-ab3b-939acad7b676",
            "title": null,
            "series_id": "34f9979a-82e2-4c3e-8780-a58af898c641",
            "publisher_id": "df4bb56a-de70-48ed-a507-b946f7ae2c0e",
            "parent_edition_id": null,
            "volumes_count": 5,
            "last_volume_number": 5,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 5694
        }
    ],
    "series": [
        {
            "id": "1b448e30-900b-4881-a1b9-683ebedcc83a",
            "title": "L'Atelier des Sorciers",
            "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "adult_content": false,
            "editions_count": 4,
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
    "boxes": [
        {
            "id": "dccc76ad-4b84-41c0-9247-b8cdba9e7279",
            "title": "Coffret intégrale",
            "number": 0,
            "release_date": "2022-09-07",
            "isbn": "9782818995730",
            "asin": "2818995736",
            "commercial_stop": false,
            "box_edition_id": "45954205-c0a0-4084-9528-ac69c64762b3",
            "box_possessions_count": 127,
            "image_url": "https://m.media-amazon.com/images/I/51VGF7ZXrxL.jpg"
        }
    ],
    "box_editions": [
        {
            "id": "b721f991-6882-4549-8a1a-20d7cf066c6d",
            "title": "Solo Leveling",
            "publisher_id": "a73823d6-1484-4bb6-b662-b7ea94aaee6b",
            "boxes_count": 1,
            "adult_content": false,
            "box_follow_editions_count": 7000
        }
    ],
    "box_volumes": []
}
```

---

## GET /v2/users/me/ad_native_planning_perso

Récupère le planning personnalisé de l'utilisateur (séries suivies uniquement).

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
{
    "ad_native_planning_persos": [
        {
            "id": "cb457ea9-9e37-42e4-8fd4-290f8ef962b3",
            "start_date": "2026-01-12",
            "end_date": "2026-02-08",
            "volume_id": "50ac476d-2bf8-4d39-8564-fe3625ba5822",
            "series_ids": [
                "a9fde975-e150-4f04-a889-153f877f3a20"
            ]
        }
    ],
    "volumes": [
        {
            "id": "50ac476d-2bf8-4d39-8564-fe3625ba5822",
            "title": null,
            "number": 5,
            "release_date": "2026-02-04",
            "isbn": "9782344072882",
            "asin": "2344072888",
            "edition_id": "e65be2d8-774f-452f-b56e-1bac8ff49b9c",
            "possessions_count": 16,
            "not_sold": false,
            "image_url": "https://m.media-amazon.com/images/I/515UEqhsrxL.jpg"
        }
    ],
    "editions": [
        {
            "id": "e65be2d8-774f-452f-b56e-1bac8ff49b9c",
            "title": null,
            "series_id": "e8c455f2-7545-44ac-b98b-1d7331c5f07d",
            "publisher_id": "5e961f4c-9954-452a-961f-4d3d922c370d",
            "parent_edition_id": null,
            "volumes_count": 7,
            "last_volume_number": 9,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 4222
        }
    ],
    "series": [
        {
            "id": "e8c455f2-7545-44ac-b98b-1d7331c5f07d",
            "title": "Tatari",
            "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "adult_content": false,
            "editions_count": 1,
            "tasks_count": 1
        }
    ]
}
```

---

## Schéma PlanningResponse

Structure de la réponse du planning des sorties.

```json
{
    "volumes": "Volume[]",
    "editions": "Edition[]",
    "series": "Series[]",
    "types": "Type[]",
    "boxes": "Box[]",
    "box_editions": "BoxEdition[]",
    "box_volumes": "BoxVolume[]"
}
```

### Champs

| Champ        | Type         | Nullable | Description                    |
|--------------|--------------|----------|--------------------------------|
| volumes      | Volume[]     | Non      | Volumes à paraître             |
| editions     | Edition[]    | Non      | Éditions associées             |
| series       | Series[]     | Non      | Séries associées               |
| types        | Type[]       | Non      | Types de séries                |
| boxes        | Box[]        | Non      | Coffrets à paraître            |
| box_editions | BoxEdition[] | Non      | Éditions de coffrets associées |
| box_volumes  | BoxVolume[]  | Non      | Volumes inclus dans coffrets   |

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
| asin                  | string  | Oui      | Code ASIN Amazon                           |
| commercial_stop       | boolean | Non      | Coffret arrêté commercialement             |
| box_edition_id        | string  | Non      | Identifiant de l'édition coffret           |
| box_possessions_count | integer | Non      | Nombre d'utilisateurs possédant ce coffret |
| image_url             | string  | Non      | URL de l'image de couverture               |

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

---

## Schéma AdNativePlanningPersoResponse

Structure de la réponse du planning personnalisé.

```json
{
    "ad_native_planning_persos": "AdNativePlanningPerso[]",
    "volumes": "Volume[]",
    "editions": "Edition[]",
    "series": "Series[]"
}
```

### Champs

| Champ                    | Type                     | Nullable | Description                    |
|--------------------------|--------------------------|----------|--------------------------------|
| ad_native_planning_persos| AdNativePlanningPerso[]  | Non      | Entrées du planning perso      |
| volumes                  | Volume[]                 | Non      | Volumes associés               |
| editions                 | Edition[]                | Non      | Éditions associées             |
| series                   | Series[]                 | Non      | Séries associées               |

---

## Schéma AdNativePlanningPerso

Structure d'une entrée de planning personnalisé.

```json
{
    "id": "string",
    "start_date": "date",
    "end_date": "date",
    "volume_id": "string",
    "series_ids": "string[]"
}
```

### Champs

| Champ      | Type     | Nullable | Description                              |
|------------|----------|----------|------------------------------------------|
| id         | string   | Non      | Identifiant unique de l'entrée           |
| start_date | date     | Non      | Date de début de la période (YYYY-MM-DD) |
| end_date   | date     | Non      | Date de fin de la période (YYYY-MM-DD)   |
| volume_id  | string   | Non      | Identifiant du volume à paraître         |
| series_ids | string[] | Non      | Identifiants des séries liées            |

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
