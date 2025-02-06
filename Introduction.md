# ezs - Introduction

## Quoi ?

[ezs](https://inist-cnrs.github.io/ezs) est un outil de manipulation de données
structurées.  
Il est Open Source et développé par l'Inist-CNRS.  

Il se présente sous la forme d'un [paquet
Node](https://www.npmjs.com/package/@ezs/core), qui peut s'utiliser comme une
bibliothèque JavaScript ou comme une commande en ligne.

Il est conçu pour traiter de gros volumes de données sans surcharger la mémoire
de l'ordinateur qui l'exécute.

## Où ?

### lodex

[Lodex](https://www.lodex.fr/) utilise ezs pour tous ses traitements de données:

- [loaders](https://github.com/Inist-CNRS/lodex/tree/master/workers/loaders)
- [exporters](https://github.com/Inist-CNRS/lodex/tree/master/workers/exporters)
- [routines](https://github.com/Inist-CNRS/lodex/tree/master/workers/routines)
- [enrichissements](https://www.lodex.fr/docs/documentation/principales-fonctionnalites-disponibles/la-creation-dun-modele/enrichissements-avec-les-web-services/#dans-lodex)
  (via les web services)
- [pré-calculs](https://www.lodex.fr/docs/documentation/pour-aller-plus-loin-2/les-pre-calculs/)

### web services TDM

Les enrichissements de lodex, ainsi que les pré-calculs déportent les
traitements vers les [web services de TDM](https://services.istex.fr/).

Ces web services exploitent la partie *serveur de traitements* d'ezs.  
Chacun d'entre eux est soit :

- écrit entièrement en ezs
- écrit partiellement en ezs

## Comment ?

### `.ini` ou `.cfg`

On reconnaît les scripts ezs (ceux qui sont lancés par la commande ou le serveur
ezs) à leur extension: `.ini` (ou `.cfg`).  

> [!NOTE]  
> Ces extensions permettent d'utiliser la colorisation syntaxique de langages de
> configuration, facilitant l'écriture de ces scripts.  
> Vous trouverez parfois cette colorisation pour le type de fichier «
> configuration apache ».

### JavaScript / Node

Pour lancer un script ezs, il faut:

- installer [node.js](https://nodejs.org/fr) (utilisation de
  [nvm](https://github.com/nvm-sh/nvm?tab=readme-ov-file#intro) recommandée)
- installer le cœur d'ezs: `npm install @ezs/core`
- installer d'éventuels compléments (voir la section suivante)
- lancer le script `script.ini`: `npx ezs script.ini < input.json`

ezs étant aussi une bibliothèque JavaScript, on peut aussi l'utiliser dans un
programme JavaScript (mais cela ne concerne que les programmeurs JavaScript).

### Playground

Pour simplifier la suite de la formation, et comme nous sommes à l'Inist, nous
utiliserons le *playground ezs*, accessible depuis le réseau Inist (donc via le
VPN aussi) à cette URL: <http://ezs-playground.daf.intra.inist.fr/>.  

Cela nous permettra d'exécuter des scripts sur de (relativement) petites
quantités de données sans avoir à installer ezs, ni à utiliser la ligne de
commande.
