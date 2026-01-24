<!-- OPENSPEC:START -->
# OpenSpec Instructions

These instructions are for AI assistants working in this project.

Always open `@/openspec/AGENTS.md` when the request:
- Mentions planning or proposals (words like proposal, spec, change, plan)
- Introduces new capabilities, breaking changes, architecture shifts, or big performance/security work
- Sounds ambiguous and you need the authoritative spec before coding

Use `@/openspec/AGENTS.md` to learn:
- How to create and apply change proposals
- Spec format and conventions
- Project structure and guidelines

Keep this managed block so 'openspec update' can refresh the instructions.

<!-- OPENSPEC:END -->

# BookSync - Instructions Projet

## Aperçu de l'objectif du projet
Application de gestion de collection de mangas permettant la synchronisation et le suivi des volumes.

## Architecture globale
Voir la documentation complète dans les fichiers référencés ci-dessous.

## Style visuel
- Interface claire et minimaliste

## Contraintes et Politiques
- **NE JAMAIS** exposer les clés API dans le code source
- Utiliser des variables d'environnement ou un fichier de configuration sécurisé

## Dépendances
- Préférer les composants Qt/PySide6 existants plutôt que d'ajouter de nouvelles bibliothèques UI

## Validation UI (après chaque développement impliquant l'interface graphique)
- Lancer l'application et vérifier les logs de la console pour les erreurs QML/Qt
- Utiliser l'arbre d'accessibilité Qt ou un dump de l'object tree pour valider la structure UI
- S'assurer que l'interface est fonctionnelle et répond au besoin développé
- Fournir un screenshot si demandé pour validation visuelle

## Documentation
- [PRD.md](docs/PRD.md) - Product Requirements Document
- [ARCHITECTURE.md](docs/ARCHITECTURE.md) - Architecture technique
- [COMMON.md](docs/COMMON.md) - Conventions communes
- [DOCKER.md](docs/DOCKER.md) - Configuration Docker

## Context7
Utilise toujours context7 lorsque j'ai besoin de génération de code, d'étapes de configuration ou d'installation,
ou de documentation de bibliothèque/API (notamment PySide6, Qt). Cela signifie que tu dois automatiquement utiliser
les outils MCP Context7 pour résoudre l'identifiant de bibliothèque et obtenir la documentation de bibliothèque
sans que j'aie à le demander explicitement.

## Note sur les spécifications
Toutes les spécifications doivent être rédigées en français, y compris les specs OpenSpec (sections Purpose et Scenarios).
Seuls les titres de Requirements doivent rester en anglais avec les mots-clés SHALL/MUST pour la validation OpenSpec.