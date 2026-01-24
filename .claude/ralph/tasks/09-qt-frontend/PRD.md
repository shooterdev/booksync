# Feature: Interface Desktop PySide6/QML (BookSync)

## Vision
Créer une réplique locale de MangaCollec en utilisant PySide6 avec QML pour gérer ma collection de mangas sur Raspberry Pi. L'application doit fonctionner hors-ligne, permettre le scan de code-barres via douchette Bluetooth, et suivre ma progression de lecture.

## Problème
- Gérer ma collection sans connexion internet (100% local)
- Scanner rapidement mes mangas avec une douchette code-barres
- Suivre où j'en suis dans mes différentes séries
- Avoir une interface moderne et fluide sur écran 1280x720

## Solution
Application desktop PySide6/QML qui se connecte aux APIs backend existantes (auth:8000, data:8001) et offre une interface similaire à MangaCollec avec thème sombre.

## Success Criteria
- Interface fonctionnelle sur Raspberry Pi (1280x720)
- Connexion/déconnexion avec JWT
- Affichage des nouveautés en grille
- Gestion de collection avec 3 onglets (Collection, Pile à lire, Envies)
- Thème sombre respectant la charte graphique

## Key Features
1. **Interface principale** - Sidebar navigation + zone de contenu
2. **Authentification** - Login avec JWT stocké localement
3. **Page Accueil** - Grille de couvertures des nouveautés
4. **Page Collection** - 3 onglets (Collection, Pile à lire, Wishlist)

## Architecture

```
booksync_app_qt/
├── src/
│   └── booksync_app_qt/
│       ├── qml/                    # UI déclarative QML
│       │   ├── main.qml            # Fenêtre principale
│       │   ├── components/         # Composants réutilisables
│       │   │   ├── MangaCard.qml   # Carte manga
│       │   │   ├── Sidebar.qml     # Navigation
│       │   │   └── ProgressBar.qml # Barre progression
│       │   ├── pages/              # Pages
│       │   │   ├── LoginPage.qml
│       │   │   ├── HomePage.qml
│       │   │   └── CollectionPage.qml
│       │   └── theme/              # Styles
│       │       └── Theme.qml       # Couleurs charte
│       ├── models/                 # QAbstractListModel
│       │   ├── manga_model.py
│       │   └── collection_model.py
│       ├── services/               # Appels API
│       │   ├── auth_service.py
│       │   └── data_service.py
│       └── main.py                 # Entry point
├── tests/
├── Dockerfile
├── docker-compose.yml
└── pyproject.toml
```

## Charte Graphique (OBLIGATOIRE)

### Mode Sombre
| Variable   | Hex              | Usage            |
|------------|------------------|------------------|
| primary    | #fc3117          | Accents, boutons |
| background | #000             | Fond app         |
| card       | rgb(18,18,18)    | Fond cartes      |
| text       | rgb(229,229,231) | Texte principal  |
| textDetail | #AAA             | Texte secondaire |
| border     | rgb(60,60,67)    | Bordures         |
| read       | #536DFE          | Statut Lu        |
| cart       | #f38f21          | Panier           |

## Phases

### Phase 1: Fondation QML
- [ ] **QT-001** - Créer structure projet booksync_app_qt avec pyproject.toml
- [ ] **QT-002** - Configurer le thème QML (Theme.qml avec couleurs charte)
- [ ] **QT-003** - Créer main.qml avec ApplicationWindow de base
- [ ] **QT-004** - Implémenter Sidebar.qml avec icônes navigation
- [ ] **QT-005** - Ajouter StackView pour navigation entre pages
- [ ] **QT-006** - Créer les modèles Python de base (QAbstractListModel)

### Phase 2: Authentification
- [ ] **QT-007** - Créer LoginPage.qml (champs username/password)
- [ ] **QT-008** - Implémenter auth_service.py (appel API 8000)
- [ ] **QT-009** - Binding QML ↔ Python pour l'authentification
- [ ] **QT-010** - Stocker JWT dans QSettings et gérer session

### Phase 3: Page Accueil
- [ ] **QT-011** - Créer MangaCard.qml (couverture + titre + badges)
- [ ] **QT-012** - Implémenter HomePage.qml avec GridView
- [ ] **QT-013** - Connecter à l'API Data (data_service.py)
- [ ] **QT-014** - Ajouter chargement asynchrone des images

### Phase 4: Page Collection
- [ ] **QT-015** - Créer CollectionPage.qml avec TabBar (3 onglets)
- [ ] **QT-016** - Implémenter onglet "Collection" (séries possédées)
- [ ] **QT-017** - Implémenter onglet "Pile à lire" (progressions)
- [ ] **QT-018** - Implémenter onglet "Envies" (wishlist)

## Out of Scope (V2+)
- Page Détails Volume
- Page Paramètres
- Page Planning
- Page Recherche
- Statistiques
- Scanner code-barres (prérequis: interface de base)
- Mode clair (priorité mode sombre)

## Context from Codebase
- **APIs existantes**: booksync_api_auth (8000), booksync_api_data (8001)
- **User stories de référence**: US-013, US-032, US-008 à US-012
- **Analyses MangaCollec**: tmp_claude/analysis_*.md
- **Écran cible**: 1280x720 pixels (Raspberry Pi)

## Technical Notes
- Python 3.11+
- PySide6 avec QML (pas widgets traditionnels)
- Architecture hexagonale adaptée au frontend
- Tests: pytest + pytest-qt (coverage min 80%)
- Docker obligatoire
