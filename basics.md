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

> [!IMPORTANT]  
> Comme tout plugin ezs, pour qu'un script ezs l'utilisant puisse fonctionner,
> il doit être installé au préalable:  
>
> ```bash
> npm install @ezs/basics
> ```
>
> Dans le contexte de lodex ou d'ezs-playground, cette installation est déjà
> effectuée, il n'y a pas à s'en préoccuper.  

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
> déclaration est encore nécessaire.  

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
> ```log
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
> Par ailleurs, il est plus efficace que `JSONString`, il est donc préférable
> d'utiliser cette possibilité de ne pas inclure `@ezs/basics`.  
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
> plugin = @ezs/basics
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

JSONL, pour [JSON Lines](https://jsonlines.org/), est une forme alternative à
JSON, qui permet d'envoyer des objets un par un, un par ligne, à un script.  
Cela a plusieurs avantages :

1. on peut utiliser des commandes UNIX comme `wc -l`
2. il est plus efficace d'analyser un seul objet qu'un objet à l'intérieur d'un
   autre (gestion de la mémoire facilitée), ce qui est le cas avec les tableaux
   d'objets qu'on traite souvent.  
3. c'est le format d'export par défaut de lodex

Les instructions de lecture et d'écriture du JSONL d'ezs font partie de son
cœur: on n'a donc pas besoin d'utiliser le *plugin* `@ezs/basics` pour s'en
servir.

> [!TIP]  
> Pour les anciens qui ont connu DILIB, c'est le même principe: une notice par
> ligne.  

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6InsgXCJ2YWx1ZVwiOiAxIH1cbnsgXCJ2YWx1ZVwiOiAyIH0iLCJzY3JpcHQiOiJbdW5wYWNrXVxuW3BhY2tdIn0=)

*Entrée*:

```jsonl
{ "value": 1 }
{ "value": 2 }
```

*Script*:

```ini
[unpack]
[pack]
```

*Sortie*:

```jsonl
{"value":1}
{"value":2}
```

## CSV (parse / string)

[CSV](https://fr.wikipedia.org/wiki/Comma-separated_values) est un format
structuré (tabulaire) répandu.  
ezs a donc des instructions pour lire et écrire ce format: `CSVParse` et
`CSVString`, du *plugin* `@ezs/basics`.  

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6ImlkLHZhbHVlXG4xLHVuZVxuMixkZXV4Iiwic2NyaXB0IjoiW3VzZV1cbiMgQ1NWUGFyc2VcbnBsdWdpbiA9IGJhc2ljc1xuXG5bQ1NWUGFyc2VdXG5cbltwYWNrXSJ9)

*Entrée*:

```csv
id,value
1,une
2,deux
```

*Script*:

```ini
[use]
# CSVParse
plugin = basics

[CSVParse]

[pack]
```

*Sortie*:

```jsonl
["id","value"]
["1","une"]
["2","deux"]
```

> [!NOTE]  
> L'interprétation du CSV par `CSVParse` est stricte, c'est-à-dire que
> l'instruction ne suppose pas de structure particulière (la ligne d'entête
> n'est pas considérée comme telle).  
> On retrouve donc une série de tableaux de valeurs, dont le premier n'a aucune
> signification particulière.  

> [!IMPORTANT]  
> Pour retrouver la structure fournie par un CSV avec une ligne d'entête
> contenant les noms des champs, il faut utiliser `CSVObject`.

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6ImlkLHZhbHVlXG4xLHVuZVxuMixkZXV4Iiwic2NyaXB0IjoiW3VzZV1cbiMgQ1NWUGFyc2VcbnBsdWdpbiA9IGJhc2ljc1xuXG5bQ1NWUGFyc2VdXG5bQ1NWT2JqZWN0XVxuW3BhY2tdIn0=)

*Entrée*:

```csv
id,value
1,une
2,deux
```

*Script*:

```ini
[use]
# CSVParse
plugin = basics

[CSVParse]
[CSVObject]
[pack]
```

*Sortie*:

```jsonl
{"id":"1","value":"une"}
{"id":"2","value":"deux"}
```

L'instruction inverse (de sérialisation) est `CSVString`, qui transforme par
défaut les objets javascript (en entrée) en chaîne de caractères au format CSV,
avec une ligne d'entête contenant les noms des champs, et le point-virgule comme
séparateur (le séparateur d'Excel français).

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6IntcImlkXCI6XCIxXCIsXCJ2YWx1ZVwiOlwidW5lXCJ9XG57XCJpZFwiOlwiMlwiLFwidmFsdWVcIjpcImRldXhcIn1cbiIsInNjcmlwdCI6Ilt1c2VdXG4jIENTVlN0cmluZ1xucGx1Z2luID0gYmFzaWNzXG5cblt1bnBhY2tdXG5cbltDU1ZTdHJpbmddXG4ifQ==)

*Entrée*:

```jsonl
{"id":"1","value":"une"}
{"id":"2","value":"deux"}
```

*Script*:

```ini
[use]
# CSVString
plugin = basics

[unpack]

[CSVString]
```

*Sortie*:

```csv
id;value
1;une
2;deux
```

> [!NOTE]  
> `CSVString` a des options importantes.  
> `separator` donne le séparateur à utiliser.  Pour produire du TSV, le plus
> simple est d'utiliser `separator = fix("\t")`

> [!WARNING]  
> Pour exprimer des caractères spéciaux (comme `\t`), il faut utiliser la
> fonction `fix()`, qui assure que l'objet javascript arrive tel quel, exprimé
> avec une syntaxe JavaScript.  
> Par défaut, ezs interprète la valeur d'un paramètre comme une chaîne de
> caractères, comme un nombre, ou comme un booléen (`true` ou `false`).  
> Pour plus de détails, voir [la documentation
> d'ezs](https://inist-cnrs.github.io/ezs/#/coding-ini?id=valeurs-statiques).  
> Donc, sans utiliser `fix()`, au lieu d'avoir une tabulation comme séparateur,
> on aurait les deux caractères `\t`, ce qui ne correspond à aucun format
> classique.  

> [!NOTE]  
> Nous ne pouvons pas prendre le temps d'explorer l'utilisation de fonctions
> dans les valeurs des paramètres des instructions ezs.  
> C'est pourtant là que réside une grande partie de la puissance d'ezs.  
> Pour plus d'information, voir le parcours complémentaire [Lodex : curation des
> données avec
> Lodash](https://360.articulate.com/review/content/8a03727a-da2c-4eed-a5f1-0f8e85cf7440/review)
> et [la documentation
> d'ezs](https://inist-cnrs.github.io/ezs/#/coding-ini?id=valeurs-dynamiques).  

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6IntcImlkXCI6XCIxXCIsXCJ2YWx1ZVwiOlwidW5lXCJ9XG57XCJpZFwiOlwiMlwiLFwidmFsdWVcIjpcImRldXhcIn1cbiIsInNjcmlwdCI6Ilt1c2VdXG4jIENTVlN0cmluZ1xucGx1Z2luID0gYmFzaWNzXG5cblt1bnBhY2tdXG5cbltDU1ZTdHJpbmddXG5zZXBhcmF0b3IgPSBmaXgoXCJcXHRcIikifQ==)

*Entrée*:

```jsonl
{"id":"1","value":"une"}
{"id":"2","value":"deux"}
```

*Script*:

```ini
[use]
# CSVString
plugin = basics

[unpack]

[CSVString]
separator = fix("\t")
```

*Sortie*:

```tsv
id	value
1	une
2	deux
```

## URL (connect)

Les enrichissements de Lodex font appel à un web service de TDM, en utilisant
l'instruction
[`URLConnect`](https://inist-cnrs.github.io/ezs/#/plugin-basics?id=urlconnect).

Elle permet typiquement d'interroger une API. Pour les connaisseurs: la méthode
HTTP employée est `POST`, et la charge (*payload*) de la requête est la
conversion de l'objet en entrée en chaîne de caractères (transformée par
`dump`).  

> [!CAUTION]  
> Comme `URLConnect` lance une requête *par objet du flux*, si vous avez
> beaucoup d'objets (de lignes, dans un `.tsv` ou dans `.jsonl`), c'est autant
> de requêtes, sur le même serveur, qui seront lancées quasi-instantanément (en
> tout cas de manière très rapprochée).  
> Il existe plusieurs solutions pour modérer cette *attaque violente* d'un
> serveur, pour éviter de s'en faire exclure.  Voir
> [expand](https://inist-cnrs.github.io/ezs/#/plugin-core?id=expand) (pour
> envoyer plusieurs lignes d'un coup, si le serveur le permet) et
> [throttle](https://inist-cnrs.github.io/ezs/#/plugin-core?id=throttle) (pour
> freiner le rythme d'envoi des requêtes).

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6InsgXCJpZFwiOiAxLCBcInZhbHVlXCI6IFwiTm9ybWFsZW1lbnQsIFRlZWZ0IGV4dHJhaXQgcGFybWkgbGVzIG1vdHMgZCd1biB0ZXh0ZSBjZXV4IHF1aSBzb250IGxlcyBwbHVzIHNw6WNpZmlxdWVzIOAgY2UgdGV4dGUuIFBvdXIgY2VsYSwgaWwgdXRpbGlzZSBkZXMgc3RhdGlzdGlxdWVzIGQndXRpbGlzYXRpb24gZGVzIG1vdHMgbGVzIHBsdXMgY291cmFudHMgZGUgbGEgbGFuZ3VlLlwifSIsInNjcmlwdCI6Ilt1c2VdXG4jIFVSTENvbm5lY3RcbnBsdWdpbiA9IGJhc2ljc1xuXG5bdW5wYWNrXVxuXG5bVVJMQ29ubmVjdF1cbnVybCA9IGh0dHBzOi8vdGVybXMtZXh0cmFjdGlvbi5zZXJ2aWNlcy5pc3RleC5mci92MS90ZWVmdC9mclxuXG5bcGFja10ifQ==)

*Entrée*:

```jsonl
{ "id": 1, "value": "Normalement, Teeft extrait parmi les mots d'un texte ceux qui sont les plus spécifiques à ce texte. Pour cela, il utilise des statistiques d'utilisation des mots les plus courants de la langue."}
```

*Script*:

```ini
[use]
# URLConnect
plugin = basics

[unpack]

[URLConnect]
url = https://terms-extraction.services.istex.fr/v1/teeft/fr

[pack]
```

*Sortie*:

```jsonl
{"id":1,"value":["teeft"]}
```

> [!NOTE]  
> Tous les web services TDM (voir la [documentation
> technique](https://openapi.services.istex.fr/?urls.primaryName=terms-extraction%20-%20Extraction%20de%20termes#/terms-extraction/post-v1-teeft-fr))
> fonctionnent de la même manière: lots d'objets avec au moins un champ `value`
> contenant l'objet du traitement, envoyé avec la méthode `POST`.  
> C'est pourquoi la donnée envoyée était sous forme d'un objet contenant un
> champ `value`.  
> Mais cela n'empêche pas d'interroger d'autres types d'API, à condition que la
> route utilisée fonctionne avec la [méthode HTTP
> `POST`](https://developer.mozilla.org/fr/docs/Web/HTTP/Methods/POST).

## Exercices: json2jsonl, jsonl2tsv, et caetera

### json2jsonl

Avec les instructions `JSONParse` et `pack`, nous sommes outillés pour créer un
script qui convertit du JSON en JSONL.  

À vous de jouer.  

<details>
<summary>
Voir la solution
</summary>

[Script](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6Ilt7IFwidmFsdWVcIjogMSB9LCB7XCJ2YWx1ZVwiOjJ9XSIsInNjcmlwdCI6Ilt1c2VdXG4jIEpTT05QYXJzZVxucGx1Z2luID0gYmFzaWNzXG5cbltKU09OUGFyc2VdXG5bcGFja10ifQ==):

```ini
[use]
# JSONParse
plugin = basics

[JSONParse]
[pack]
```

</details>

### jsonl2tsv

Pour convertir du JSONL en TSV, tout ce qu'il nous faut, ce sont les
instructions `unpack` et `CSVString` (mais cette fois il faudra utiliser
quelques paramètres).

<details>
<summary>
Voir la solution
</summary>

[Script](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6IntcImlkXCI6XCJpdW5cIixcInZhbHVlXCI6MX1cbntcImlkXCI6XCJkZXV4XCIsXCJ2YWx1ZVwiOjJ9XG4iLCJzY3JpcHQiOiJbdXNlXVxuIyBDU1ZTdHJpbmdcbnBsdWdpbiA9IGJhc2ljc1xuXG5bdW5wYWNrXVxuW0NTVlN0cmluZ11cbnNlcGFyYXRvciA9IGZpeChcIlxcdFwiKSJ9):

```ini
[use]
# CSVString
plugin = basics

[unpack]
[CSVString]
separator = fix("\t")
```

</details>

### csv2tsv

La conversion d'un CSV français (dont le séparateur est un point-virgule) et
dont certains champs contiennent des double-quotes en TSV vous posera-t-elle des
problèmes ?  
Si c'est le cas, n'oubliez pas de consulter la [documentation
d'ezs](https://inist-cnrs.github.io/ezs/#/plugin-basics?id=csvparse).  

Pour tester, utilisez ce CSV français:

```csv
id;value
"1";"""citation"""
"2";"normal"
```

<details>
<summary>
Voir la solution
</summary>

[Script](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6ImlkO3ZhbHVlXG5cIjFcIjtcIlwiXCJjaXRhdGlvblwiXCJcIlxuXCIyXCI7XCJub3JtYWxcIiIsInNjcmlwdCI6Ilt1c2VdXG4jIENTVlBhcnNlIENTVk9iamVjdCBDU1ZTdHJpbmdcbnBsdWdpbiA9IGJhc2ljc1xuXG5bQ1NWUGFyc2VdXG5zZXBhcmF0b3IgPSA7XG5xdW90ZSA9IGZpeCgnXCInKVxuXG5bQ1NWT2JqZWN0XVxuXG5bQ1NWU3RyaW5nXVxuc2VwYXJhdG9yID0gZml4KFwiXFx0XCIpXG4ifQ==):

```ini
[use]
# CSVParse CSVObject CSVString
plugin = basics

[CSVParse]
separator = ;
quote = fix('"')

[CSVObject]

[CSVString]
separator = fix("\t")
```

</details>
