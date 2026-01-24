# prompt 1

```text
Pour faire ces modifications, voici une application qu'on va se baser. L'application Booksync doit faire exactement tout
ce que cette application web peut faire. Voici la liste des liens de différentes fonctionnalités que doit contenir l'application. 

page news
    - https://www.mangacollec.com
page collection
    section pile a lire
        - https://www.mangacollec.com/user/shooterdev/reading
    section collection
        - https://www.mangacollec.com/collection
    section completed
        - https://www.mangacollec.com/user/shooterdev/missing
    section wish
        - https://www.mangacollec.com/user/shooterdev/wish
    section loan
        - https://www.mangacollec.com/loan
        - https://www.mangacollec.com/loan/create
    section statistics
        - https://www.mangacollec.com/user/shooterdev/collection/statistics
        - https://www.mangacollec.com/user/shooterdev/collection/statistics/publishers
        - https://www.mangacollec.com/user/shooterdev/collection/statistics/kinds
        - https://www.mangacollec.com/user/shooterdev/collection/history
        - https://www.mangacollec.com/user/shooterdev/reading/history
page planning
    section personalise
        - https://www.mangacollec.com/user/shooterdev/planning
    section tout
        - https://www.mangacollec.com/planning
    section news
        - https://www.mangacollec.com/planning/news
    section coffrets
        - https://www.mangacollec.com/planning/boxes
    section 
        - 
page recherche
    - https://www.mangacollec.com/series
    - https://www.mangacollec.com/authors
    - https://www.mangacollec.com/publishers
page panier
    - https://www.mangacollec.com/cart
page volume
    - https://www.mangacollec.com/volumes/5822ea5c-e86d-4ec1-bf45-69826de86a31
page serie
    - https://www.mangacollec.com/series/a02cf154-af6c-4f08-9a7a-32f7bc229ac8
page edition
    - https://www.mangacollec.com/editions/d56047bf-5855-4757-9f2f-539f9922e8db
page author
    - https://www.mangacollec.com/authors/e6cc4590-0b5e-4122-9428-b9b185bdb221
page publisher
    - https://www.mangacollec.com/publishers/5e961f4c-9954-452a-961f-4d3d922c370d
page settings
    - https://www.mangacollec.com/users/settings

Voici les différentes pages que j'ai trouvées. Il se peut qu'il y en a d'autres, mais l'essentiel de l'application est 
contenu dans ces pages. 
Certains liens de l'utilisateur, je me suis connecter. Donc si tu as besoin des informations de login, n'hésite pas 
à me demander des informations de type cookies pour avoir les authentifications Ça peut t'aider. 
pour y accéder à certaines pages. Donc, créer un sub-agent pour chacun de ces pages. Ainsi, pour avoir 
une vision globale de la page, attention, 
c'est une application en React, donc il faut exécuter du JavaScript pour voir le contenu de la page. Donc, 
décris chaque page de ses fonctionnalités, des redirections vers d'autres pages, etc. 

Assure-toi de bien vérifier le contenu de la page car certains éléments devront être transformés en widgets. 
Donc il faut faire attention sur ça, de bien délimiter les sections de la page pour créer des widgets. 

Attention de ne rien inventer. Décris seulement ce que tu vois explicitement. Ne crée pas de fonctionnalités fantômes. 

Et ensuite, avec les informations que tu as eues, modifie le fichier. prd.md  
```
---

## pronts 2

```
Pour chaque page url suivante utilise le mcp de chrome avec une resolution de 1280x720. 
page news/home
    - https://www.mangacollec.com
page collection
    section pile a lire
        - https://www.mangacollec.com/user/shooterdev/reading
    section collection
        - https://www.mangacollec.com/collection
    section completed
        - https://www.mangacollec.com/user/shooterdev/missing
    section wish
        - https://www.mangacollec.com/user/shooterdev/wish
    section loan
        - https://www.mangacollec.com/loan
        - https://www.mangacollec.com/loan/create
    section statistics
        - https://www.mangacollec.com/user/shooterdev/collection/statistics
        - https://www.mangacollec.com/user/shooterdev/collection/statistics/publishers
        - https://www.mangacollec.com/user/shooterdev/collection/statistics/kinds
        - https://www.mangacollec.com/user/shooterdev/collection/history
        - https://www.mangacollec.com/user/shooterdev/reading/history
page planning
    section personalise
        - https://www.mangacollec.com/user/shooterdev/planning
    section tout
        - https://www.mangacollec.com/planning
    section news
        - https://www.mangacollec.com/planning/news
    section coffrets
        - https://www.mangacollec.com/planning/boxes
    section 
        - 
page recherche
    - https://www.mangacollec.com/series
    - https://www.mangacollec.com/authors
    - https://www.mangacollec.com/publishers
page panier
    - https://www.mangacollec.com/cart
page volume
    - https://www.mangacollec.com/volumes/5822ea5c-e86d-4ec1-bf45-69826de86a31
page serie
    - https://www.mangacollec.com/series/a02cf154-af6c-4f08-9a7a-32f7bc229ac8
page edition
    - https://www.mangacollec.com/editions/d56047bf-5855-4757-9f2f-539f9922e8db
page author
    - https://www.mangacollec.com/authors/e6cc4590-0b5e-4122-9428-b9b185bdb221
page publisher
    - https://www.mangacollec.com/publishers/5e961f4c-9954-452a-961f-4d3d922c370d
page settings
    - https://www.mangacollec.com/users/settings
    
cree un sub-agent pour Regardez chacun des pages. Avec l'aide du MCP de Chrome, les pages avec une résolution de 1280x720 
afin d'avoir tout les information utile pour cree les widget avec leur dimention exact pour les buton 
**inportent** les dimention doit recpecter au pixel pres avect la resolution de la fenetre de 1280x720
Décrit pour chaque page les infformation qui sont Essentiels. Comme les requêtes API, que la page a bession pour fonctionner
avec cest information utilise un sub-agent alimenter chaque user-story 


Ensuite, créez les epic dans @docs/epic. pour chaque user_story En utilisant des sub-adjants. mes avant cree le template pour les epic

```

dark
    --colors-primary: #fc3117;
    --colors-background: #000;
    --colors-border: rgb(60, 60, 67);
    --colors-card: rgb(18, 18, 18);
    --colors-text: rgb(229, 229, 231);
    --colors-text-detail: #AAA;
    --colors-icon: #666;
    --colors-read: #536DFE;
    --colors-cart: #f38f21;
    --colors-state-hovered: rgba(255, 255, 255, 0.1);
    --colors-state-pressed: rgba(255, 255, 255, 0.15);
light
    --colors-primary: #CF000A;
    --colors-background: #FFF;
    --colors-border: rgb(224, 224, 224);
    --colors-card: #FFF;
    --colors-text: rgb(28, 28, 30);
    --colors-text-detail: #777;
    --colors-icon: #BBB;
    --colors-read: #3D5AFE;
    --colors-cart: #F5B027;
    --colors-state-hovered: rgba(0, 0, 0, 0.07);
    --colors-state-pressed: rgba(0, 0, 0, 0.12);


 Rajoute les règles suivantes.
- service auth_api dans booksync_api_auth/
- service data_api dans booksync_api_data/
- service prediction dans booksync_api_prediction/
- app dans booksync_app_qt/
