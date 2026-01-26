# Prêts — API Mangacollec

Documentation des endpoints liés aux prêts et emprunteurs.

---

## Endpoints

| Méthode | Endpoint               | Description                     |
|---------|------------------------|---------------------------------|
| POST    | `/v1/loans_multiple`   | Créer des prêts                 |
| DELETE  | `/v1/loans_multiple`   | Supprimer des prêts             |
| POST    | `/v2/borrowers`        | Créer un emprunteur/storage     |
| DELETE  | `/v2/borrowers/{id}`   | Supprimer un emprunteur/storage |

> **Schémas associés :** Voir [collection.md](collection.md) pour les schémas `Loan` et `Borrower`.

---

## POST /v1/loans_multiple

Crée des prêts de volumes.

### Corps de la requête

```json
{
    "loans": [
        {
            "borrower_id": "7a2d73ed-e99a-42fd-8e06-ee3a0c158569",
            "possession_id": "1619df2a-9993-4947-877d-9c914c903333"
        }
    ]
}
```

### Réponse

```json
{
    "loans": [
        {
            "id": "b88a90cb-1a78-4fc9-b510-bfa7f18621f0",
            "borrower_id": "7a2d73ed-e99a-42fd-8e06-ee3a0c158569",
            "possession_id": "1619df2a-9993-4947-877d-9c914c903333",
            "created_at": "2026-01-26T21:05:08.503Z"
        }
    ]
}
```

---

## DELETE /v1/loans_multiple

Supprime des prêts.

### Corps de la requête

```json
{
    "loans": [
        {
            "id": "c639891b-be9d-485f-9354-95057fe4d196"
        }
    ]
}
```

### Réponse

```json
{
    "loans": [
        {
            "id": "c639891b-be9d-485f-9354-95057fe4d196"
        }
    ]
}
```

### Réponse erreur

```json
{
    "loans": [
        {
            "id": "b88a90cb-1a78-4fc9-b510-bfa7f18621f0",
            "errors": [
                "This loans doesn't exist"
            ]
        }
    ]
}
```

---

## POST /v2/borrowers

Crée un emprunteur ou lieu de stockage.

### Corps de la requête

```json
{
    "category": "person",
    "title": "Nom de l'emprunteur"
}
```

| Champ    | Type   | Requis | Description                          |
|----------|--------|--------|--------------------------------------|
| category | string | Oui    | Type : "person" ou "storage"         |
| title    | string | Oui    | Nom de l'emprunteur ou du lieu       |

### Réponse

```json
{
    "id": "f4b030fe-735e-4ead-a449-c1290aa6d180",
    "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
    "title": "test",
    "category": "person",
    "created_at": "2026-01-26T21:12:24.566Z",
    "updated_at": "2026-01-26T21:12:24.566Z"
}
```

### Réponse erreur

```json
{
    "errors": [
        "La validation a échoué : Category test is not a valid category"
    ]
}
```

---

## DELETE /v2/borrowers/{id}

Supprime un emprunteur ou lieu de stockage.

### Paramètres

| Paramètre | Type   | Requis | Description                 |
|-----------|--------|--------|-----------------------------|
| `id`      | string | Oui    | Identifiant de l'emprunteur |

### Réponse

**Code 204** : Aucun contenu retourné.

### Réponse erreur (404)

```json
{
    "errors": [
        "Couldn't find this record"
    ]
}
```

---

## Schéma LoansCreateRequest

Corps de la requête pour créer des prêts.

```json
{
    "loans": [
        {
            "borrower_id": "string",
            "possession_id": "string"
        }
    ]
}
```

### Champs

| Champ         | Type   | Requis | Description                        |
|---------------|--------|--------|------------------------------------|
| borrower_id   | string | Oui    | Identifiant de l'emprunteur        |
| possession_id | string | Oui    | Identifiant de la possession       |

---

## Schéma LoansCreateResponse

Réponse après création de prêts.

```json
{
    "loans": "Loan[]"
}
```

### Champs

| Champ | Type   | Nullable | Description     |
|-------|--------|----------|-----------------|
| loans | Loan[] | Non      | Prêts créés     |

---

## Schéma LoansDeleteRequest

Corps de la requête pour supprimer des prêts.

```json
{
    "loans": [
        {
            "id": "string"
        }
    ]
}
```

### Champs

| Champ | Type   | Requis | Description               |
|-------|--------|--------|---------------------------|
| id    | string | Oui    | Identifiant du prêt       |

---

## Schéma LoansDeleteResponse

Réponse après suppression de prêts.

```json
{
    "loans": "LoanDeleteResult[]"
}
```

### Champs

| Champ | Type               | Nullable | Description                       |
|-------|--------------------|----------|-----------------------------------|
| loans | LoanDeleteResult[] | Non      | Résultats de suppression          |

---

## Schéma LoanDeleteResult

Structure du résultat de suppression d'un prêt.

```json
{
    "id": "string",
    "errors": "string[] | undefined"
}
```

### Champs

| Champ  | Type     | Nullable | Description                          |
|--------|----------|----------|--------------------------------------|
| id     | string   | Non      | Identifiant du prêt                  |
| errors | string[] | Oui      | Erreurs si la suppression a échoué   |

---

## Schéma BorrowerCreateRequest

Corps de la requête pour créer un emprunteur.

```json
{
    "category": "string",
    "title": "string"
}
```

### Champs

| Champ    | Type   | Requis | Description                          |
|----------|--------|--------|--------------------------------------|
| category | string | Oui    | Type : "person" ou "storage"         |
| title    | string | Oui    | Nom de l'emprunteur ou du lieu       |

---

## Schéma BorrowerCreateResponse

Réponse après création d'un emprunteur.

```json
{
    "id": "string",
    "user_id": "string",
    "title": "string",
    "category": "string",
    "created_at": "datetime",
    "updated_at": "datetime"
}
```

### Champs

| Champ      | Type     | Nullable | Description                       |
|------------|----------|----------|-----------------------------------|
| id         | string   | Non      | Identifiant unique                |
| user_id    | string   | Non      | Identifiant de l'utilisateur      |
| title      | string   | Non      | Nom de l'emprunteur ou du lieu    |
| category   | string   | Non      | Catégorie : "person" ou "storage" |
| created_at | datetime | Non      | Date de création (ISO 8601)       |
| updated_at | datetime | Non      | Date de mise à jour (ISO 8601)    |
