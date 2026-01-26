# Lectures — API Mangacollec

Documentation des endpoints liés aux lectures de volumes.

---

## Endpoints

| Méthode | Endpoint              | Description              |
|---------|-----------------------|--------------------------|
| POST    | `/v1/reads_multiple`  | Marquer des volumes lus  |
| DELETE  | `/v1/reads_multiple`  | Supprimer des lectures   |

> **Schémas associés :** Voir [collection.md](collection.md) pour les schémas `Read` et `ReadEdition`.

---

## POST /v1/reads_multiple

Marque plusieurs volumes comme lus.

### Corps de la requête

```json
[
    {
        "volume_id": "dcf1f103-48d0-4a0a-b20a-acc20f656f04"
    }
]
```

### Réponse

```json
{
    "reads": [
        {
            "id": "0994509d-1227-411d-ac18-73708b3f6371",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "volume_id": "dcf1f103-48d0-4a0a-b20a-acc20f656f04",
            "created_at": "2025-10-26T19:57:46.199Z"
        }
    ],
    "read_editions": [
        {
            "id": "3a15a949-28f8-4ab9-bdc2-94df79b84d6d",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "edition_id": "1e68b837-be95-4baa-a38f-09c082777c04",
            "reading": true,
            "created_at": "2025-10-26T19:57:46.192Z"
        }
    ]
}
```

---

## DELETE /v1/reads_multiple

Supprime des lectures de volumes.

### Corps de la requête

```json
[
    {
        "id": "0994509d-1227-411d-ac18-73708b3f6371"
    }
]
```

### Réponse

```json
{
    "reads": [
        {
            "id": "0994509d-1227-411d-ac18-73708b3f6371",
            "deleted": true
        }
    ],
    "read_editions": [
        {
            "id": "3a15a949-28f8-4ab9-bdc2-94df79b84d6d",
            "deleted": true
        }
    ]
}
```

---

## Schéma ReadsCreateRequest

Corps de la requête pour marquer des volumes comme lus.

```json
[
    {
        "volume_id": "string"
    }
]
```

### Champs

| Champ     | Type   | Requis | Description                    |
|-----------|--------|--------|--------------------------------|
| volume_id | string | Oui    | Identifiant du volume à marquer lu |

---

## Schéma ReadsCreateResponse

Réponse après ajout de lectures.

```json
{
    "reads": "Read[]",
    "read_editions": "ReadEdition[]"
}
```

### Champs

| Champ         | Type          | Nullable | Description                          |
|---------------|---------------|----------|--------------------------------------|
| reads         | Read[]        | Non      | Lectures créées                      |
| read_editions | ReadEdition[] | Non      | Suivis de lecture par édition créés  |

---

## Schéma ReadsDeleteRequest

Corps de la requête pour supprimer des lectures.

```json
[
    {
        "id": "string"
    }
]
```

### Champs

| Champ | Type   | Requis | Description                  |
|-------|--------|--------|------------------------------|
| id    | string | Oui    | Identifiant de la lecture    |

---

## Schéma ReadsDeleteResponse

Réponse après suppression de lectures.

```json
{
    "reads": "DeletedItem[]",
    "read_editions": "DeletedItem[]"
}
```

### Champs

| Champ         | Type          | Nullable | Description                           |
|---------------|---------------|----------|---------------------------------------|
| reads         | DeletedItem[] | Non      | Lectures supprimées                   |
| read_editions | DeletedItem[] | Non      | Suivis de lecture par édition supprimés |

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
