# Volumes — API Mangacollec

Documentation des endpoints liés aux volumes (tomes).

---

## Endpoints

| Méthode | Endpoint               | Description              |
|---------|------------------------|--------------------------|
| GET     | `/v2/volumes/news`     | Dernières sorties        |
| GET     | `/v2/volumes/{id}`     | Détail d'un volume       |

---

## GET /v2/volumes/news

Récupère la liste des dernières sorties.

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
{
    "volumes": [{
            "id": "5732a254-c687-4868-b095-2e94e1144ae2",
            "title": null,
            "number": 1,
            "release_date": "2026-01-28",
            "isbn": "9791039142571",
            "asin": "B0FSQYJF73",
            "edition_id": "9611c5af-7d9f-49cc-ad00-3bb5a247f0a3",
            "possessions_count": 15,
            "not_sold": false,
            "image_url": "https://m.media-amazon.com/images/I/413uN9lkgdL.jpg"
        }
    ],
    "editions": [{
            "id": "9611c5af-7d9f-49cc-ad00-3bb5a247f0a3",
            "title": "Nouvelle édition",
            "series_id": "9e9afafc-81af-46b9-9f99-3da11add27a0",
            "publisher_id": "df4bb56a-de70-48ed-a507-b946f7ae2c0e",
            "parent_edition_id": null,
            "volumes_count": 2,
            "last_volume_number": null,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 562
        }
    ],
    "series": [{
            "id": "9e9afafc-81af-46b9-9f99-3da11add27a0",
            "title": "Please Save My Earth",
            "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "adult_content": false,
            "editions_count": 2,
            "tasks_count": 1
        }
    ],
    "types": [{
            "id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "title": "Manga",
            "to_display": false
        }
    ],
    "boxes": [{
            "id": "574a5170-7125-4e88-86fc-492e4b96a0ad",
            "title": "Edition Collector Tome 16",
            "number": 0,
            "release_date": "2025-12-04",
            "isbn": "9791042020842",
            "asin": "B0FC1R7LJ5",
            "commercial_stop": false,
            "box_edition_id": "5c480ec5-60c3-4320-a78e-149d21d09f8a",
            "box_possessions_count": 3355,
            "image_url": "https://m.media-amazon.com/images/I/71Rj7jZKNwL.jpg"
        }
    ],
    "box_editions": [{
            "id": "5c480ec5-60c3-4320-a78e-149d21d09f8a",
            "title": "Oshi no Ko",
            "publisher_id": "2f064f84-a653-48c1-b1f6-27a694bb7ec6",
            "boxes_count": 1,
            "adult_content": false,
            "box_follow_editions_count": 3750
        }
    ],
    "box_volumes": [],
    "native_ad_volume_home_first": {
        "id": "1402958a-67c0-45f1-84b9-9cbcf13f6b10",
        "volume_id": "ab9202dc-c43a-498f-b82e-ead6736b6dc7",
        "title": "Découvrir",
        "start_date": "2025-10-20",
        "end_date": "2025-10-26"
    }
}
```

---

## GET /v2/volumes/{id}

Récupère le détail d'un volume spécifique.

### Paramètres

| Paramètre | Type   | Requis | Description           |
|-----------|--------|--------|-----------------------|
| `id`      | string | Oui    | Identifiant du volume |

### Réponse

```json
{
    "volumes": [{
            "id": "6e22eae9-2afd-45ba-8c26-d10d4224d5bf",
            "title": null,
            "number": 1,
            "release_date": "2016-06-01",
            "isbn": "9782818936238",
            "asin": "2818936233",
            "edition_id": "aa4da196-ccda-4b33-af3f-6f4b75145c6b",
            "possessions_count": 18645,
            "not_sold": false,
            "image_url": "https://images-eu.ssl-images-amazon.com/images/I/51pTipXj2mL.jpg",
            "nb_pages": 160,
            "content": "<p><strong>Trahison, monstres et rivalités : comment survivre dans un RPG avec juste un bouclier ?</strong></p>\r\n<p>Naofumi est projeté dans un monde proche en tout lieu d’un jeu de rôle d’heroic fantasy. Mais alors que d’autres héros ont été dotés d’armes offensives redoutables, Naofumi hérite d’un bouclier aux capacités limitées pour progresser dans ce jeu où le danger peut surgir à chaque instant. Trahi par sa partenaire et vilipendé par la population, le jeune homme ne peut désormais compter que sur lui-même pour survivre dans cet univers hostile… et peut-être sur une jeune fille désœuvrée aux ressources insoupçonnées.</p>"
        }
    ],
    "editions": [{
            "id": "aa4da196-ccda-4b33-af3f-6f4b75145c6b",
            "title": null,
            "series_id": "3ce0e5bb-77ea-4186-b0d1-f335e851c313",
            "publisher_id": "c1866645-afb4-44dd-bdce-420606488352",
            "parent_edition_id": null,
            "volumes_count": 28,
            "last_volume_number": null,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 22239
        }
    ],
    "publishers": [
        {
            "id": "c1866645-afb4-44dd-bdce-420606488352",
            "title": "Doki Doki",
            "closed": false,
            "editions_count": 210,
            "no_amazon": false
        }
    ],
    "series": [
        {
            "id": "3ce0e5bb-77ea-4186-b0d1-f335e851c313",
            "title": "The Rising of the Shield Hero",
            "type_id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "adult_content": false,
            "editions_count": 5,
            "tasks_count": 2
        }
    ],
    "types": [
        {
            "id": "106f524e-7283-44b8-aa84-25e9a7fb1f7d",
            "title": "Manga",
            "to_display": false
        }
    ],
    "box_volumes": [
        {
            "id": "14924bcc-b6ee-4db1-9908-ed79b681583f",
            "box_id": "a8832564-df86-4ba2-8447-846a8f4a0e4f",
            "volume_id": "6e22eae9-2afd-45ba-8c26-d10d4224d5bf",
            "included": true
        }
    ],
    "boxes": [{
            "id": "340c9d91-d7f8-4ad7-9bb0-f35686ec8936",
            "title": "Tome 1 + bagdes collector",
            "number": 0,
            "release_date": "2025-09-10",
            "isbn": "9791041114368",
            "asin": "B0F2MF5HZJ",
            "commercial_stop": false,
            "box_edition_id": "1b7d6a0d-51a2-482f-8dd1-0da5e8ac487f",
            "box_possessions_count": 23,
            "image_url": "https://m.media-amazon.com/images/I/51n0FE83abL.jpg"
        }
    ],
    "box_editions": [
        {
            "id": "dd7b6ebc-f372-4968-934f-6c8f7cd95e76",
            "title": "The Rising of the Shield Hero",
            "publisher_id": "c1866645-afb4-44dd-bdce-420606488352",
            "boxes_count": 3,
            "adult_content": false,
            "box_follow_editions_count": 247
        }
    ]
}
```

---

## Schéma VolumesNewsResponse

Structure de la réponse pour les dernières sorties.

```json
{
    "volumes": "Volume[]",
    "editions": "Edition[]",
    "series": "Series[]",
    "types": "Type[]",
    "boxes": "Box[]",
    "box_editions": "BoxEdition[]",
    "box_volumes": "BoxVolume[]",
    "native_ad_volume_home_first": "NativeAdVolumeHomeFirst | null"
}
```

### Champs

| Champ                       | Type                          | Nullable | Description                    |
|-----------------------------|-------------------------------|----------|--------------------------------|
| volumes                     | Volume[]                      | Non      | Dernières sorties volumes      |
| editions                    | Edition[]                     | Non      | Éditions associées             |
| series                      | Series[]                      | Non      | Séries associées               |
| types                       | Type[]                        | Non      | Types de séries                |
| boxes                       | Box[]                         | Non      | Dernières sorties coffrets     |
| box_editions                | BoxEdition[]                  | Non      | Éditions de coffrets           |
| box_volumes                 | BoxVolume[]                   | Non      | Volumes inclus dans coffrets   |
| native_ad_volume_home_first | NativeAdVolumeHomeFirst       | Oui      | Pub native volume accueil      |

---

## Schéma VolumeDetailResponse

Structure de la réponse pour le détail d'un volume.

```json
{
    "volumes": "VolumeDetail[]",
    "editions": "Edition[]",
    "publishers": "Publisher[]",
    "series": "Series[]",
    "types": "Type[]",
    "box_volumes": "BoxVolume[]",
    "boxes": "Box[]",
    "box_editions": "BoxEdition[]"
}
```

### Champs

| Champ        | Type           | Nullable | Description                    |
|--------------|----------------|----------|--------------------------------|
| volumes      | VolumeDetail[] | Non      | Volume avec détails complets   |
| editions     | Edition[]      | Non      | Éditions associées             |
| publishers   | Publisher[]    | Non      | Éditeurs                       |
| series       | Series[]       | Non      | Séries associées               |
| types        | Type[]         | Non      | Types de séries                |
| box_volumes  | BoxVolume[]    | Non      | Coffrets contenant ce volume   |
| boxes        | Box[]          | Non      | Coffrets                       |
| box_editions | BoxEdition[]   | Non      | Éditions de coffrets           |

---

## Schéma Volume

Structure d'un volume (version liste).

```json
{
    "id": "string",
    "title": "string | null",
    "number": "integer",
    "release_date": "date",
    "isbn": "string",
    "asin": "string | null",
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
| asin              | string  | Oui      | Code ASIN Amazon                          |
| edition_id        | string  | Non      | Identifiant de l'édition                  |
| possessions_count | integer | Non      | Nombre d'utilisateurs possédant ce volume |
| not_sold          | boolean | Non      | Indique si le volume n'est plus vendu     |
| image_url         | string  | Non      | URL de l'image de couverture              |

---

## Schéma VolumeDetail

Structure d'un volume avec détails complets.

```json
{
    "id": "string",
    "title": "string | null",
    "number": "integer",
    "release_date": "date",
    "isbn": "string",
    "asin": "string | null",
    "edition_id": "string",
    "possessions_count": "integer",
    "not_sold": "boolean",
    "image_url": "string",
    "nb_pages": "integer | null",
    "content": "string | null"
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
| asin              | string  | Oui      | Code ASIN Amazon                          |
| edition_id        | string  | Non      | Identifiant de l'édition                  |
| possessions_count | integer | Non      | Nombre d'utilisateurs possédant ce volume |
| not_sold          | boolean | Non      | Indique si le volume n'est plus vendu     |
| image_url         | string  | Non      | URL de l'image de couverture              |
| nb_pages          | integer | Oui      | Nombre de pages                           |
| content           | string  | Oui      | Description/synopsis (HTML)               |

---

## Schéma NativeAdVolumeHomeFirst

Structure d'une publicité native volume.

```json
{
    "id": "string",
    "volume_id": "string",
    "title": "string",
    "start_date": "date",
    "end_date": "date"
}
```

### Champs

| Champ      | Type   | Nullable | Description                           |
|------------|--------|----------|---------------------------------------|
| id         | string | Non      | Identifiant unique de la pub          |
| volume_id  | string | Non      | Identifiant du volume promu           |
| title      | string | Non      | Titre de la pub (ex: "Découvrir")     |
| start_date | date   | Non      | Date de début (YYYY-MM-DD)            |
| end_date   | date   | Non      | Date de fin (YYYY-MM-DD)              |

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
