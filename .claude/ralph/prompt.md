# Ralph Agent Instructions

## Your Task

You are an autonomous AI coding agent running in a loop. Each iteration, you implement ONE user story from the PRD.

## Execution Sequence

1. **Read Context**
   - Read the PRD (prd.json) to understand all user stories
   - Read progress.txt to see patterns and learnings from previous iterations
   - Identify the **highest priority** story where `passes: false`

2. **Check Git Branch**
   - Verify you're on the correct branch (see `branchName` in prd.json)
   - If not, checkout the branch: `git checkout <branchName>` or create it

3. **Implement ONE Story**
   - Focus on implementing ONLY the selected story
   - Follow the acceptance criteria exactly
   - Make minimal changes to achieve the goal

4. **Verify Quality**
   - Run typecheck (if applicable): `pnpm tsc --noEmit` or `npm run typecheck`
   - Run tests (if applicable): `pnpm test` or `npm test`
   - Fix any issues before proceeding

5. **Commit Changes**
   - Stage your changes: `git add .`
   - Commit with format: `feat: [STORY-ID] - [Title]`
   - Example: `feat: US-001 - Add login form validation`

6. **Update PRD**
   - Update prd.json to mark the story as `passes: true`
   - Add any notes about the implementation

7. **Log Learnings**
   - Append to progress.txt with format:

```
## [Date] - [Story ID]: [Title]
- What was implemented
- Files changed
- **Learnings:**
  - Patterns discovered
  - Gotchas encountered
---
```

## Codebase Patterns

Check the TOP of progress.txt for patterns discovered by previous iterations:
- Follow existing patterns
- Add new patterns when you discover them
- Update patterns if they're outdated

## Stop Condition

**If ALL stories have `passes: true`**, output this exact text:

<promise>COMPLETE</promise>

This signals the loop to stop.

## Critical Rules

- 🛑 NEVER implement more than ONE story per iteration
- 🛑 NEVER skip the verification step (typecheck/tests)
- 🛑 NEVER commit if tests are failing
- ✅ ALWAYS check progress.txt for patterns FIRST
- ✅ ALWAYS update prd.json after implementing
- ✅ ALWAYS append learnings to progress.txt


  Pages Principales                                                                     
  #: 1                                                                                  
  Écran: Accueil / Nouveautés                                                           
  Description: Page d'accueil affichant les dernières sorties et actualités manga avec  
    grille de couvertures et barre de navigation latérale                               
                                                
  #: 2                                                                                  
  Écran: Collection                                                                     
  Description: Page principale avec 8 sous-sections (voir ci-dessous)                   
                                                
  #: 3                                                                                  
  Écran: Planning                                                                       
  Description: Calendrier des sorties avec 4 onglets (voir ci-dessous)                  
                                                
  #: 4                                                                                  
  Écran: Recherche                                                                      
  Description: Moteur de recherche global avec onglets TITRES / AUTEURS / EDITEURS      
                                                
  #: 5                                                                                  
  Écran: Panier                                                                         
  Description: Centre de gestion des achats planifiés avec calcul automatique du budget 
                                                
  #: 6                                                                                  
  Écran: Paramètres (Compte)                                                            
  Description: Configuration du compte utilisateur, préférences et déconnexion          
  Sous-sections Collection (7.2)                                                        
  #: 2.1                                                                                
  Écran: Pile à lire                                                                    
  Description: Vue centralisée de la progression de lecture avec statistiques (tomes    
    lus/à lire) et liste des séries en cours                                            
                                                
  #: 2.2                                                                                
  Écran: Collection                                                                     
  Description: Vue exhaustive de tous les volumes possédés avec options de tri et       
    galeries de couvertures                                                             
                                                
  #: 2.3                                                                                
  Écran: Compléter                                                                      
  Description: Identification automatique des tomes manquants dans les séries           
    partiellement possédées                                                             
                                                
  #: 2.4                                                                                
  Écran: Envies (Wishlist)                                                              
  Description: Liste de souhaits des séries/volumes à acquérir                          
                                                
  #: 2.5                                                                                
  Écran: Prêts                                                                          
  Description: Suivi des volumes prêtés ou stockés ailleurs avec formulaire de création 
                                                
  #: 2.6                                                                                
  Écran: Statistiques                                                                   
  Description: Tableau de bord analytique avec répartition par éditeurs et genres       
    (camembert, barres)                                                                 
                                                
  #: 2.7                                                                                
  Écran: Historique Collection                                                          
  Description: Journal chronologique des ajouts organisé par année et mois              
                                                
  #: 2.8                                                                                
  Écran: Historique Lecture                                                             
  Description: Journal chronologique des lectures organisé par semaine                  
  Sous-sections Planning (7.3)                                                          
  #: 3.1                                                                                
  Écran: Personnalisé                                                                   
  Description: Calendrier filtré sur les séries suivies uniquement                      
                                                
  #: 3.2                                                                                
  Écran: Tout                                                                           
  Description: Vue exhaustive de toutes les sorties, tous éditeurs confondus            
                                                
  #: 3.3                                                                                
  Écran: Nouveautés                                                                     
  Description: Vitrine des nouvelles séries (Tome 1, guidebooks, one-shots)             
                                                
  #: 3.4                                                                                
  Écran: Coffrets                                                                       
  Description: Éditions collector, coffrets, packs spéciaux et éditions limitées        
  Fiches Détaillées                                                                     
  #: 7                                                                                  
  Écran: Fiche Volume                                                                   
  Description: Informations complètes sur un tome : couverture, prix, résumé, détails   
    (ISBN, pages, date), boutons d'action                                               
                                                
  #: 8                                                                                  
  Écran: Fiche Série                                                                    
  Description: Informations sur une série : type, genres, auteur(s), liste des éditions 
    disponibles                                                                         
                                                
  #: 9                                                                                  
  Écran: Fiche Édition                                                                  
  Description: Vue détaillée d'une édition avec grille de tous les volumes et           
    statistiques                                                                        
                                                
  #: 10                                                                                 
  Écran: Fiche Auteur                                                                   
  Description: Informations sur un auteur/mangaka avec grille de ses œuvres et rôles    
                                                
  #: 11                                                                                 
  Écran: Fiche Éditeur                                                                  
  Description: Catalogue d'un éditeur : dernières sorties, prochaines sorties, catalogue
        