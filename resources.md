# Ressources

Cette section regroupe les ressources utiles pour approfondir vos connaissances
sur ezs et continuer à l'utiliser efficacement après la formation.

## playground

Le [playground ezs](http://ezs-playground.daf.intra.inist.fr/) est un outil en
ligne accessible depuis le réseau Inist (ou via VPN) qui permet de tester
rapidement des scripts ezs sans avoir à installer quoi que ce soit.

Il offre une interface simple avec&nbsp;:

- une zone pour entrer les données d'entrée,
- une zone pour écrire le script ezs,
- une zone affichant le résultat de l'exécution.

C'est un excellent moyen de prototyper et d'expérimenter avec ezs avant
d'intégrer vos scripts dans des projets plus importants.

> [!WARNING]  
> Ce n'est pas pour autant un environnement de production&nbsp;: la taille des
> données en entrée est limitée, et certains plugins ne sont pas disponibles.

## npm

ezs est distribué sous forme de paquets npm. Le cœur du système est disponible
dans le paquet [@ezs/core](https://www.npmjs.com/package/@ezs/core).

Pour installer ezs et ses plugins&nbsp;:

```bash
# Installation du cœur d'ezs
npm install @ezs/core

# Installation de plugins supplémentaires
npm install @ezs/basics
npm install @ezs/analytics
# etc.
```

Vous pouvez consulter tous les plugins officiels sur npm en recherchant les
paquets commençant par [@ezs/](https://www.npmjs.com/search?q=%40ezs%2F).

## documentation

La documentation officielle d'ezs est disponible à l'adresse suivante&nbsp;:
<https://inist-cnrs.github.io/ezs/>

Elle contient&nbsp;:

- une présentation générale d'ezs,
- la documentation de tous les plugins officiels,
- des exemples d'utilisation,
- des informations sur l'API JavaScript.

La documentation est organisée par plugin, ce qui permet de retrouver facilement
les instructions disponibles dans chacun d'eux.

## parcours complémentaire Lodash

Pour approfondir vos connaissances sur l'utilisation de Lodash dans ezs, un
parcours complémentaire est disponible : [Lodex : curation des données avec
Lodash](https://360.articulate.com/review/content/8a03727a-da2c-4eed-a5f1-0f8e85cf7440/review).

Ce parcours vous permettra de&nbsp;:

- maîtriser les fonctions Lodash les plus utiles dans le contexte d'ezs,
- comprendre comment utiliser le chaînage de fonctions Lodash,
- apprendre à manipuler efficacement des données complexes,
- voir des exemples concrets d'utilisation dans des scripts ezs.

> [!NOTE]  
> Comme mentionné dans la section "le cas Lodash" du document sur les usages
> avancés, la fonction `compute()` abordée dans ce parcours complémentaire a été
> supprimée depuis la version 3.11.0 de `@ezs/core`.  

Pour compléter ce parcours, la [documentation officielle de
Lodash](https://lodash.com/docs/) est une ressource précieuse, bien qu'il faille
garder à l'esprit que dans ezs, Lodash est utilisé en [mode
chaîné](https://lodash.com/docs/4.17.15#chain).

## github (ezs, lodex/ezsLodex)

Le code source d'ezs et de ses plugins est disponible sur GitHub&nbsp;:

- [ezs (cœur et plugins officiels)](https://github.com/Inist-CNRS/ezs)&nbsp;:
  contient le code source du cœur d'ezs et de la plupart des plugins officiels,  
- [lodex/ezsLodex](https://github.com/Inist-CNRS/lodex/tree/master/packages/ezsLodex)&nbsp;:
  contient le code source du plugin ezs spécifique à Lodex, avec des
  instructions adaptées aux besoins particuliers de cette application.  

Ces dépôts sont ouverts aux contributions. Si vous identifiez des bugs ou des
améliorations possibles, n'hésitez pas à ouvrir une issue ou à proposer une
pull request.

Pour les développeurs souhaitant créer leurs propres plugins ezs, le code source
existant constitue une excellente référence pour comprendre la structure et les
conventions à suivre.
