# Auteurs — API Mangacollec

Documentation des endpoints liés aux auteurs.

---

## Endpoints

| Méthode | Endpoint             | Description              |
|---------|----------------------|--------------------------|
| GET     | `/v2/authors`        | Recherche d'auteurs      |
| GET     | `/v2/authors/{id}`   | Détail d'un auteur       |

---

## GET /v2/authors

Recherche et liste des auteurs.

### Paramètres

| Paramètre | Type | Requis | Description |
|-----------|------|--------|-------------|
|           |      |        |             |

### Réponse

```json
{
    "authors": [
        {
            "id": "...",
            "name": "...",
            "first_name": "...",
            "tasks_count": 32
        }
    ]
}
```

---

## GET /v2/authors/{id}

Récupère le détail d'un auteur spécifique.

### Paramètres

| Paramètre | Type   | Requis | Description             |
|-----------|--------|--------|-------------------------|
| `id`      | string | Oui    | Identifiant de l'auteur |

### Réponse

```json
{
    "authors": [
        {
            "id": "...",
            "name": "...",
            "first_name": "...",
            "tasks_count": 32
        }
    ],
    "tasks": [
        {
            "id": "...",
            "job_id": "...",
            "series_id": "...",
            "author_id": "..."
        }
    ],
    "jobs": [
        {
            "id": "...",
            "title": "..."
        }
    ],
    "series": [
        {
            "id": "...",
            "title": "...",
            "type_id": "...",
            "adult_content": false,
            "editions_count": 7,
            "tasks_count": 1
        }
    ],
    "editions": [
        {
            "id": "...",
            "title": null,
            "series_id": "...",
            "publisher_id": "...",
            "parent_edition_id": null,
            "volumes_count": 4,
            "last_volume_number": null,
            "commercial_stop": false,
            "not_finished": false,
            "follow_editions_count": 10818
        }
    ],
    "volumes": [
        {
            "id": "...",
            "title": null,
            "number": 2,
            "release_date": "2025-06-20",
            "isbn": null,
            "asin": null,
            "edition_id": "...",
            "possessions_count": 139,
            "not_sold": true,
            "image_url": "..."
        }
    ]
}
```

---

## Schéma AuthorsListResponse

Structure de la réponse pour la liste des auteurs.

```json
{
    "authors": "Author[]"
}
```

### Champs

| Champ   | Type     | Nullable | Description        |
|---------|----------|----------|--------------------|
| authors | Author[] | Non      | Liste des auteurs  |

---

## Schéma AuthorDetailResponse

Structure de la réponse pour le détail d'un auteur.

```json
{
    "authors": "Author[]",
    "tasks": "Task[]",
    "jobs": "Job[]",
    "series": "Series[]",
    "editions": "Edition[]",
    "volumes": "Volume[]"
}
```

### Champs

| Champ    | Type      | Nullable | Description                 |
|----------|-----------|----------|-----------------------------|
| authors  | Author[]  | Non      | Auteur                      |
| tasks    | Task[]    | Non      | Tâches (œuvres) de l'auteur |
| jobs     | Job[]     | Non      | Métiers                     |
| series   | Series[]  | Non      | Séries de l'auteur          |
| editions | Edition[] | Non      | Éditions des séries         |
| volumes  | Volume[]  | Non      | Volumes des éditions        |

---

## Schéma Author

Structure d'un auteur.

```json
{
    "id": "string",
    "name": "string",
    "first_name": "string | null",
    "tasks_count": "integer"
}
```

### Champs

| Champ       | Type    | Nullable | Description                       |
|-------------|---------|----------|-----------------------------------|
| id          | string  | Non      | Identifiant unique de l'auteur    |
| name        | string  | Non      | Nom de famille                    |
| first_name  | string  | Oui      | Prénom                            |
| tasks_count | integer | Non      | Nombre de tâches/œuvres associées |

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

## Schéma Volume

Structure d'un volume.

```json
{
    "id": "string",
    "title": "string | null",
    "number": "integer",
    "release_date": "date",
    "isbn": "string | null",
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
| isbn              | string  | Oui      | Code ISBN-13                              |
| asin              | string  | Oui      | Code ASIN Amazon                          |
| edition_id        | string  | Non      | Identifiant de l'édition                  |
| possessions_count | integer | Non      | Nombre d'utilisateurs possédant ce volume |
| not_sold          | boolean | Non      | Indique si le volume n'est plus vendu     |
| image_url         | string  | Non      | URL de l'image de couverture              |
