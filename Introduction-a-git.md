# Introduction à Git

[Git](https://fr.wikipedia.org/wiki/Git) est un logiciel de gestion de version permettant de faciliter le travail collaboratif et d'augmenter la qualité du produit livré au client.

## 01 - Concepts fondamentaux

### 01 - Commit

Comme Git est un logiciel de gestion de versions, il utilise des états - appelés `commits` - du code source pour suivre la différentes versions.
Chacun de ces `commits` est identifié par une chaîne de caractère unique générée par l'algorithme `SHA-1`. Cela donnerait par exemple : `88c2656ff484858dc7e3a3a91e115b0d9d4bf2ad`.
Un `commit` représente un état du code source. C'est-à-dire une certaine structuration de la base de code et du contenu de ses fichiers.
Il s'agit d'un état indépendant qui n'a besoin d'aucun autre objet (au sens Git) pour exister. Cela veut dire qu'un commit n'est pas intrinsèquement lié à une branche (`branch`), à un `tag` ou à tout autre objet.
Comme un identifiant `SHA-1` n'est pas suffisamment explicite, chaque `commit` peut avoir un message associé. Généralement les serveurs `Git` imposent l'affectation d'un message à chaque `commit`.

Voici une représentation d'exemple d'un `commit`: `1a8d9fec8f5db2454573fce5e3c36276be03e9b7 Resolved an issue with version 0.0.6`.

On utilise généralement une version abrégée de l'identifiant pour augmenter la lisibilité : `1a8d9fe Resolved an issue with version 0.0.6`.

### 02 - Branche (branch)

Les branches (`branch`) `Git` sont des objet représentant une succession de `commit`s.
Chaque branche porte une nom unique. Par convention la branche par défaut est nommée `master`. Il s'agit généralement de la branche utilisée en production.
Une branche suit un raisonnement _vertical_ en cascade. Il est **essentiel de comprendre qu'une branche est une succession ordonnée de `commits`**.

Exemple de branche :

`4427194 Resolves #CSN-94`
`5eee35b Resolves #CSN-92`
`0552eb4 Resolves #CSN-89`
`33cf1e1 Resolved merge conflict`
`08df4ce Resolves #CSN-87`

Une branche classe toujours les `commit`s par ordre chronologique décroissant.
Dans cet exemple le `commit` `4427194 Resolves #CSN-94` est donc le plus récent.

Les `commit`s référencés au sein d'une branche suivent toujours un ordre chronologique d'ajout et l'intégrité de la branche doit toujours être préservé.
Cela veut dire que les `commit` se suivent dans un ordre cohérent et que les conflits entre les modifications effectuées par deux `commit`s doivent impérativement être résolus avant que les deux `commit`s ne puissent être référencés sur la branche. Ces conflits sont généralement appelés `merge conflict`s et seront abordés plus loin.

### 03 - Tag

Certains `commit`s revêtent une importance spécifique. Il s'agit par exemple du `commit` marquant l'état de la base de code pour une version donnée.

Par exemple : `d9ce8c8 Version 0.0.6`.

On voit que ce `commit` est important car il marque la version `0.0.6` de la base de code.
Il serait très peu commode de devoir mémoriser ou répertorier les identifiants `SHA-1` des commits importants. Pour pallier à ce problème il est possible d'affecter un `tag` à un commit.
On peut par exemple affecter le tag `v0.0.6` au commit `d9ce8c8`.

Cela donnerait donc le format suivant :

`d9ce8c8 (tag: v0.0.6) Version 0.0.6`

### 04 - Dépôt (repository)

Un dépôt ou `repository` - simplement `repo` - ou encore `remote` est un ensemble d'objets `Git` visant à gérer les versions d'un même projet.

Généralement un `repository` est un ensemble de branches.

On peut par exemple avoir un `repository` ayant deux branches : `master` et `development`.
Chaque branche peut évoluer de façon indépendante et être synchronisées (`merge`d) de temps en temps.

Un dépôt est également appelé `remote` car il s'agit d'une base de code distante. Théoriquement il existe une base de code locale mais cette copie n'est qu'un `index` et non un dépôt _per se_.
Il est possible qu'un même référentiel local `Git` suivent différents `repo` mais cela est un cas assez rare et utilisé uniquement dans des architectures très complexes.

### Zone de transit

La zone de transit est un concept simple. Il s'agit de fichiers mis en attente pour être `commit`.
Pour comprendre la zone de transit se référer à la partie liée aux commandes et manipulations `Git`.


## 02 - Commandes

### Initialiser un référentiel local vierge

Pour initialiser un nouveau référentiel local il suffit de lancer les commandes suivantes :

`git init` <= Cette commande va créer un dossier `.git` contenant l'ensemble des fichiers nécessaires au fonctionnement du référentiel. C'est théoriquement la seule commande obligatoire.
`git add .` <= Cette commande va ajouter l'ensemble des **_fichiers et dossiers contenant des fichiers_** autres que `.git` à la zone de transit.
`git commit -m "Initialization"` <= Cette commande va permettre de récupérer les fichiers placés en zone de transit pour les agrégés en un `commit`.
`git remote add origin <remote_reference>` <= Cette commande permet de déclarer que nous souhaite utiliser un nouveau `repo` dont la référence est `<remote_reference>` pour ce référentiel.
`git push -u origin <branch>` <= Cette commande permet d'envoyer le `commit` précédemment créé localement vers le `repo`. Il est nécessaire d'utiliser l'argument `-u` pour spécifier la branche sur laquelle nous souhaitons intégrer notre `commit`.

### git add

La commande `git add <path>` permet d'ajouter des fichiers à la zone de transit.
L'argument `<path>` est relatif à l'emplacement de lancement de la commande.

Admettons une structure de dossiers telle que :

/-|
  |-.git
  |-src/---------|main/         
  |-build        |public/-------|frontend.js
  |-README.md    |app.js        |style.css

Si nous lançons la commande `git add .` dans le dossier `src` alors `app.js` et le dossier `public` seront ajoutés mais pas le dossier `main`. Ce dernier ne contenant aucun fichier il ne sera pas ajouté à la zone de transit.
Comme expliqué précédemment, seuls les dossiers contenant des fichiers peuvent être référencés.
Il aurait également été possible - toujours depuis le dossier `src` - de lancer la commande `git add app.js` pour n'ajouter que le fichier `app.js` ou même `git add public` pour n'ajouter que le dossier `public`.

### git commit

La commande `git commit` permet d'agréger les fichiers en zone de transit au sein d'un commit.
Cette commande n'a d'effet que sur les fichiers en zone de transit.

Exemple :

Zone de transit :
`src/app.js`

Fichiers hors zone de transit :
`src/public/frontend.js`
`src/public/style.css`

Dans ce cas seul le fichier `src/app.js` sera agrégé au sein d'un commit. Les autres fichiers resteront en état modifié ou non suivi.

**/!\ Attention /!\\**

La commande `git commit` connaît notamment un paramètre `m` permettant d'indiquer un message.
Ce message n'étant pas obligatoire _per se_ dans la logique `Git`, la plupart des serveurs `Git` imposent l'ajout d'un message.
Il suffit d'exécuter `git commit -m "Raison explicite de mon commit"`. Le message doit impérativement être entouré de quotes `"` s'il contient plusieurs mots.

### git push

La commande `git push` permet de _pousser_ les `commit`s référencés localement vers le `repo`.
Cette commande tentera de mettre à jour la branche distante liée à la branche locale.

**/!\ Attention /!\\**

La commande `git push` sera rejétée si la branche locale n'est pas au moins aussi avancée que la branche distante. Il faudra alors récupérer les changements effectués sur la branche distante (par le biais de la commande `git pull`) avant d'exécuter la commande `git push`.

### git pull

La commande `git pull` permet de récupérer les `commit`s référencés sur la branche distante et non-référencés localement.
Cela permet de mettre à jour la base de code.

Dans le cas où les changements distants entreraient en conflit avec les changements locaux, un `merge conflict` surviendrait.


## 03 - Sous-module (submodule)

Un `submodule` est une dépendance `Git`.

Exemple : On souhaite utiliser le même code pour afficher un bandeau invitant à l'acceptation des cookies sur différents projets sans lien.

On peut alors créer un dépôt `Git` spécifique pour ce bandeau puis le référencer dans tous les sites ayant besoin de l'utiliser.
Cela permet d'utiliser la même base de code sur l'ensemble des projets. Si un changement est réalisé sur le sous-module il pourra être répercuté sur l'ensemble des projets l'utilisant de façon automatisée et uniforme.
Il est également possible de travailler directement sur le code source du module directement depuis le projet le référençant. **_Il s'agit de la différence principale entre les `submodule`s et les `subtree`s (non évoqués ici)_**.

### 01 - Fonctionnement basique

Un sous-module est référencé dans l'index du `repo` "parent". Il ne faut pas accorder trop d'importance à la hierarchie entre le parent et les sous-modules. Il ne s'agit que d'un simple système de dépendances.
Cela veut dire que **tout dépôt `Git` est susceptible d'utiliser et d'être utilisé comme sous-module sans que cela ne l'impact directement**.

### 02 - Commandes

#### git submodule init

La commande `git submodule init` permet d'initialiser les sous-modules déjà référencés dans la base de code.
L'initialisation d'un sous-module ne permet pas de récupérer la base de code du sous-module. Uniquement son `index`.

#### git submodule update

La commande `git submodule update` permet de mettre à jour un sous-module déjà initialisé pour récupérer la base de code du sous-module.
La mise à jour s'effectue par rapport au `repo` parent.
