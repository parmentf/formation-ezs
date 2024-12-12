# Instructions d'entrée-sortie

Les scripts reçoivent en entrée une chaîne de caractères (les informations
structurées sont souvent stockées dans des fichiers `.csv`, `.xml`, `.json`,
...).

Par défaut, ezs ne sait pas interpréter les chaînes de caractères, mais traite
directement des objets JavaScript.

Il dispose donc d'un *plugin* appelé `@ezs/basics` permettant de transformer des
chaînes de caractères en objets JavaScript.

De la même manière, par défaut, ezs produit des objets JavaScript, pas des
chaînes de caractères.  
Or, en ligne de commande on s'attend à recevoir le contenu d'un fichier, donc du
texte.  
Le *plugin* `@ezs/basics` fournit donc les instructions qui permettent de
retransformer ces objets en chaînes de caractères formatées.  

> [!IMPORTANT]  
> Pour utiliser les instructions d'entrée-sortie, il faut déclarer, au début du
> script, que celui-ci va utiliser le *plugin* `@ezs/basics`.  
>
> ```ini
> [use]
> plugin = @ezs/basics
> ```

> [!TIP]  
> On peut abréger le nom des *plugins* commençant par `@ezs/` (les *plugins*
> officiels, maintenus par l'Inist), en omettant cette première partie.  
>
> ```ini
> [use]
> plugin = basics
> ```
>
> Cependant, il est plus prudent d'exposer le nom complet du *plugin* (cette
> abréviation n'est là que pour des raisons historiques).  
> Cela permet d'éviter une éventuelle ambiguïté.  

> [!NOTE]  
> La documentation d'ezs fournit la description des instructions de
> `@ezs/basics`: <https://inist-cnrs.github.io/ezs/#/plugin-basics>

## JSON (parse / dump)

Pour analyser (*parse* en anglais) une chaîne de caractères représentant un
objet [JSON](https://www.json.org/json-fr.html), `@ezs/basics` fournit
l'instruction `JSONParse`.  

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6Ilt7IFwidmFsdWVcIjogMSB9LCB7XCJ2YWx1ZVwiOjJ9XSIsInNjcmlwdCI6Ilt1c2VdXG4jIEpTT05QYXJzZSBKU09OU3RyaW5nXG5wbHVnaW4gPSBAZXpzL2Jhc2ljc1xuXG5bSlNPTlBhcnNlXVxuXG5bSlNPTlN0cmluZ11cbiJ9)

*Entrée*:

```json
[{ "value": 1 }, {"value":2}]
```

*Script*:

```ini
[use]
# JSONParse JSONString
plugin = @ezs/basics

[JSONParse]

[JSONString]
```

*Sortie*:

```json
[{"value":1},{"value":2}]
```

> [!TIP]  
> Nous avons ajouté un *commentaire* juste au-dessus de la ligne `plugin =
> @ezs/basics` pour signaler quelles instructions nécessitent l'utilisation du
> *plugin*.  
> Cela permet, au cours de la vie de scripts plus longs, de s'assurer que cette
> déclaration est toujours nécessaire.  

> [!IMPORTANT]  
> Quand un *plugin* est déclaré, il doit impérativement être installé, sous
> peine de faire planter le script.  
> Pour cela, comme on le fait pour `@ezs/core`, on utilise `npm`:
>
> ```bash
> npm install @ezs/basics
> ```

> [!WARNING]  
> Au cours de vos interactions avec ezs, vous pouvez rencontrer une
> sortie ressemblant à:
>
> ```txt
> [object Object]
> [object Object]
> ```
>
> C'est typiquement le cas quand aucune instruction de formatage n'est utilisée
> en fin de script (ici `JSONString`).  
> C'est la manière dont JavaScript *sérialise* ses objets par défaut.  

> [!NOTE]  
> Il est conseillé de remplacer `JSONString` par `dump`, qui est inclus dans ezs
> par défaut.  
> Par ailleurs, il est plus efficace que `JSONString`, il n'y a donc aucune
> raison de se priver de cette possibilité de ne pas inclure `@ezs/basics`.  
>
> Le script devient alors:
>
> ```ini
> [use]
> # JSONParse
> plugin = @ezs/basics
>
> [JSONParse]
>
> [dump]
> ```

> [!TIP]  
> L'instruction [`dump`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=dump)
> (tout comme `JSONString`) a un paramètre `indent` qu'on peut mettre à `true`
> pour indenter le résultat.
>
> ```ini
> [use]
> # JSONParse
> plugin = basics
> 
> [JSONParse]
> 
> [dump]
> indent = true
> ```
>
> produit le résultat:
>
> ```json
> [{
>     "value": 1
> },
> {
>     "value": 2
> }]
> ```

## JSONL (unpack / pack)

## CSV (parse / string)

## URL (connect / stream)

## Exercices: json2jsonl, jsonl2tsv, et caetera
