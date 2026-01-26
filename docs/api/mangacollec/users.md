# Utilisateurs — API Mangacollec

Documentation des endpoints liés aux utilisateurs.

---

## Endpoints

| Méthode | Endpoint                         | Description                     |
|---------|----------------------------------|---------------------------------|
| GET     | `/v1/users/me`                   | Utilisateur courant             |
| GET     | `/v2/user/{username}/collection` | Collection publique utilisateur |

---

## GET /v1/users/me

Récupère les informations de l'utilisateur authentifié.

### Paramètres

Aucun paramètre requis.

### Réponse

```json
{
    "id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
    "email": "platevoet.vincent@icloud.com",
    "username": "shooterdev",
    "notification_email": true,
    "setting_collection_order": "title",
    "created_at": "2019-03-07T15:34:25.044Z",
    "confirmed_at": "2025-10-26T21:13:21.599Z",
    "confirmation_sent_at": "2025-10-26T21:13:01.852Z",
    "unconfirmed_email": null,
    "certify_adult": true,
    "possessions_count": 1812,
    "ad_home_banner": true,
    "ad_native_home_first": true,
    "ad_native_planning_perso": true,
    "is_premium": true,
    "subscriptions": [
        {
            "id": "7f74f97e-acc5-4d6d-86fc-aace42eed507",
            "user_id": "1f32e2fc-c7c3-4dd3-9f18-e0fcdb075295",
            "app": "android",
            "product_id": "com.mangacollec.premium.yearly",
            "original_purchase_date": "2022-05-25T17:10:44.000Z",
            "expires_date": "2023-07-08T17:09:49.000Z",
            "auto_renewing": false,
            "created_at": "2022-05-25T17:11:06.509Z",
            "updated_at": "2023-07-10T04:59:43.478Z",
            "cancellation_date": null
        }
    ]
}
```

---

## GET /v2/user/{username}/collection

Récupère la collection publique d'un utilisateur.

### Paramètres

| Paramètre  | Type   | Requis | Description       |
|------------|--------|--------|-------------------|
| `username` | string | Oui    | Nom d'utilisateur |

### Réponse

> Structure identique à `/v2/users/me/collection` (voir [collection.md](collection.md))

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
    ]
}
```

---

## Schéma User

Structure de l'utilisateur authentifié.

```json
{
    "id": "string",
    "email": "string",
    "username": "string",
    "notification_email": "boolean",
    "setting_collection_order": "string",
    "created_at": "datetime",
    "confirmed_at": "datetime | null",
    "confirmation_sent_at": "datetime | null",
    "unconfirmed_email": "string | null",
    "certify_adult": "boolean",
    "possessions_count": "integer",
    "ad_home_banner": "boolean",
    "ad_native_home_first": "boolean",
    "ad_native_planning_perso": "boolean",
    "is_premium": "boolean",
    "subscriptions": "Subscription[]"
}
```

### Champs

| Champ                    | Type           | Nullable | Description                              |
|--------------------------|----------------|----------|------------------------------------------|
| id                       | string         | Non      | Identifiant unique de l'utilisateur      |
| email                    | string         | Non      | Adresse email                            |
| username                 | string         | Non      | Nom d'utilisateur                        |
| notification_email       | boolean        | Non      | Notifications par email activées         |
| setting_collection_order | string         | Non      | Ordre de tri collection (ex: "title")    |
| created_at               | datetime       | Non      | Date de création du compte (ISO 8601)    |
| confirmed_at             | datetime       | Oui      | Date de confirmation email               |
| confirmation_sent_at     | datetime       | Oui      | Date d'envoi du mail de confirmation     |
| unconfirmed_email        | string         | Oui      | Email en attente de confirmation         |
| certify_adult            | boolean        | Non      | Certification contenu adulte             |
| possessions_count        | integer        | Non      | Nombre de volumes possédés               |
| ad_home_banner           | boolean        | Non      | Affichage pub bannière accueil           |
| ad_native_home_first     | boolean        | Non      | Affichage pub native accueil             |
| ad_native_planning_perso | boolean        | Non      | Affichage pub planning perso             |
| is_premium               | boolean        | Non      | Utilisateur premium                      |
| subscriptions            | Subscription[] | Non      | Liste des abonnements                    |

---

## Schéma Subscription

Structure d'un abonnement.

```json
{
    "id": "string",
    "user_id": "string",
    "app": "string",
    "product_id": "string",
    "original_purchase_date": "datetime",
    "expires_date": "datetime",
    "auto_renewing": "boolean",
    "created_at": "datetime",
    "updated_at": "datetime",
    "cancellation_date": "datetime | null"
}
```

### Champs

| Champ                  | Type     | Nullable | Description                                   |
|------------------------|----------|----------|-----------------------------------------------|
| id                     | string   | Non      | Identifiant unique de l'abonnement            |
| user_id                | string   | Non      | Identifiant de l'utilisateur                  |
| app                    | string   | Non      | Plateforme (ex: "android", "ios")             |
| product_id             | string   | Non      | ID produit (ex: "com.mangacollec.premium...") |
| original_purchase_date | datetime | Non      | Date d'achat initial (ISO 8601)               |
| expires_date           | datetime | Non      | Date d'expiration (ISO 8601)                  |
| auto_renewing          | boolean  | Non      | Renouvellement automatique actif              |
| created_at             | datetime | Non      | Date de création (ISO 8601)                   |
| updated_at             | datetime | Non      | Date de mise à jour (ISO 8601)                |
| cancellation_date      | datetime | Oui      | Date d'annulation                             |
