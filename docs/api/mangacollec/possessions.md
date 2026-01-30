# Possessions — API Mangacollec

Documentation des endpoints liés aux possessions et au suivi d'éditions.

---

## Endpoints

| Méthode | Endpoint                   | Description                   |
|---------|----------------------------|-------------------------------|
| POST    | `/v1/possessions_multiple` | Ajouter des possessions       |
| DELETE  | `/v1/possessions_multiple` | Supprimer des possessions     |
| POST    | `/v1/follow_editions`      | Suivre une édition            |
| DELETE  | `/v1/follow_editions/{id}` | Arrêter de suivre une édition |

> **Schémas associés :** Voir [collection.md](collection.md) pour les schémas `Possession` et `FollowEdition`.

---

## POST /v1/possessions_multiple

Ajoute plusieurs volumes à la collection de l'utilisateur.

### Corps de la requête

```json
[
    {
        "volume_id": "fdb3365e-b18d-45b3-9ab0-09b3b73b9367"
    }
]
```

### Réponse

```json
{
    "possessions": [
        {
            "id": "f32038b6-e9b9-4427-ae6d-94b15df1a022",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "volume_id": "fdb3365e-b18d-45b3-9ab0-09b3b73b9367",
            "created_at": "2025-10-26T19:46:02.397Z"
        }
    ],
    "follow_editions": [
        {
            "id": "c43fb28c-944c-48cd-806a-6ac2f7fe241e",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "edition_id": "bd982e24-525d-47cc-a152-7582e5f8236d",
            "following": true,
            "created_at": "2025-10-26T19:46:02.385Z"
        }
    ]
}
```

> **Note** : Le `follow_edition` retourné ici ne contient pas `updated_at`, contrairement au schéma complet (voir [collection.md](collection.md#schéma-followedition)).

---

## DELETE /v1/possessions_multiple

Supprime plusieurs volumes de la collection de l'utilisateur.

### Corps de la requête

```json
[
    {
        "id": "c3c66009-f8c5-4d4e-abcc-a34591f29166"
    }
]
```

### Réponse

```json
{
    "possessions": [
        {
            "id": "c3c66009-f8c5-4d4e-abcc-a34591f29166",
            "deleted": true
        }
    ],
    "follow_editions": [
        {
            "id": "9e0b1776-546e-4d1f-8856-e22ba47dec68",
            "deleted": true
        }
    ],
    "loans": []
}
```

---

## POST /v1/follow_editions

Suit une édition pour l'utilisateur.

### Corps de la requête

```json
{
    "edition_id": "bd982e24-525d-47cc-a152-7582e5f8236d",
    "following": true
}
```

### Réponse

```json
{
    "id": "bc325464-e110-49fa-b346-726d116f8975",
    "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
    "edition_id": "bd982e24-525d-47cc-a152-7582e5f8236d",
    "following": true,
    "created_at": "2025-10-26T20:45:42.486Z",
    "updated_at": "2025-10-26T20:45:42.486Z"
}
```

---

## DELETE /v1/follow_editions/{id}

Arrête de suivre une édition.

### Paramètres

| Paramètre | Type   | Requis | Description                  |
|-----------|--------|--------|------------------------------|
| `id`      | string | Oui    | Identifiant du suivi édition |

### Réponse

**Code 204** : Aucun contenu retourné.

---

## Schéma PossessionsCreateRequest

Corps de la requête pour ajouter des possessions.

```json
[
    {
        "volume_id": "string"
    }
]
```

### Champs

| Champ     | Type   | Requis | Description                   |
|-----------|--------|--------|-------------------------------|
| volume_id | string | Oui    | Identifiant du volume à ajouter |

---

## Schéma PossessionsCreateResponse

Réponse après ajout de possessions.

```json
{
    "possessions": "Possession[]",
    "follow_editions": "FollowEdition[]"
}
```

### Champs

| Champ           | Type            | Nullable | Description                        |
|-----------------|-----------------|----------|------------------------------------|
| possessions     | Possession[]    | Non      | Possessions créées                 |
| follow_editions | FollowEdition[] | Non      | Suivis d'éditions créés            |

---

## Schéma PossessionsDeleteRequest

Corps de la requête pour supprimer des possessions.

```json
[
    {
        "id": "string"
    }
]
```

### Champs

| Champ | Type   | Requis | Description                       |
|-------|--------|--------|-----------------------------------|
| id    | string | Oui    | Identifiant de la possession      |

---

## Schéma PossessionsDeleteResponse

Réponse après suppression de possessions.

```json
{
    "possessions": "DeletedItem[]",
    "follow_editions": "DeletedItem[]",
    "loans": "Loan[]"
}
```

### Champs

| Champ           | Type          | Nullable | Description                          |
|-----------------|---------------|----------|--------------------------------------|
| possessions     | DeletedItem[] | Non      | Possessions supprimées               |
| follow_editions | DeletedItem[] | Non      | Suivis d'éditions supprimés          |
| loans           | Loan[]        | Non      | Prêts associés (supprimés ou vides)  |

---

## Schéma DeletedItem

Structure d'un élément supprimé.

```json
{
    "id": "string",
    "deleted": true
}
```

### Champs

| Champ   | Type    | Nullable | Description                    |
|---------|---------|----------|--------------------------------|
| id      | string  | Non      | Identifiant de l'élément       |
| deleted | boolean | Non      | Toujours `true` pour confirmé  |

---

## Schéma FollowEditionCreateRequest

Corps de la requête pour suivre une édition.

```json
{
    "edition_id": "string",
    "following": "boolean"
}
```

### Champs

| Champ      | Type    | Requis | Description                    |
|------------|---------|--------|--------------------------------|
| edition_id | string  | Oui    | Identifiant de l'édition       |
| following  | boolean | Oui    | `true` pour suivre             |
