# Instructions essentielles

ezs est fourni avec des instructions par défaut, qui font donc partie du paquet
npm appelé `@ezs/core` (celui qu'on installe quand on veut utiliser ezs).  
C'est une partie de ces instructions « essentielles » que nous allons aborder.  

## assign

Si on veut modifier le contenu d'un champ de l'objet courant (ou bien en ajouter
un), c'est l'instruction
[`assign`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=assign) à laquelle
on pense en premier.  

Elle affecte une valeur à un champ de l'objet courant (désigné par le paramètre
`path`), en écrasant éventuellement sa valeur précédente par le produit du
paramètre `value`.

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6InsgXCJpZFwiOiAxIH1cbnsgXCJpZFwiOiAyIH0iLCJzY3JpcHQiOiJbdW5wYWNrXVxuXG5bYXNzaWduXVxucGF0aCA9IHVuXG52YWx1ZSA9IDFcblxuW2R1bXBdXG5pbmRlbnQgPSB0cnVlIn0=)

*Entrée*:

```jsonl
{ "id": 1 }
{ "id": 2 }
```

*Script*:

```ini
[unpack]

[assign]
path = un
value = 1

[dump]
indent = true
```

*Sortie*:

```json
[{
    "id": 1,
    "un": 1
},
{
    "id": 2,
    "un": 1
}]
```

> [!TIP]  
> En général, quand une instruction ezs a un paramètre `path`, il respecte la
> syntaxe [JSONPath](https://jsonpath.com/) (sans commencer par `$`), en
> [notation pointée](https://goessner.net/articles/JsonPath/index.html#e2) à la
> manière de la fonction [`get`](https://lodash.com/docs/4.17.15#get) de Lodash.

> [!NOTE]  
> Comme le script n'utilise que des instructions du cœur d'ezs
> ([`@ezs/core`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=usage)), il
> ne comprend pas l'instruction `use` pour indiquer quel *plugin* il va
> utiliser.

> [!NOTE]  
> Le plus souvent, et c'est le cas ici, les paramètres `path` et `value` sont
> répétables.  
> C'est-à-dire qu'au lieu d'utiliser plusieurs instructions `assign` les unes à
> la suite des autres pour modifier ou créer plusieurs champs dans le même
> objet, on peut répéter les paramètres `path` et `value` sous la même
> instruction `assign`.
>
> Au lieu de
>
> ```ini
> [assign]
> path = un
> value = 1
>
> [assign]
> path = deux
> value = 2
> ```
>
> On peut écrire:
>
> ```ini
> [assign]
> path = un
> path = deux
>
> value = 1
> value = 2
> ```
>
> Chaque paramètre `value` correspondra au `path` de même rang.  
> Mais il est bien plus lisible (et donc maintenable) de les rassembler ainsi:
>
> ```ini
> [assign]
> path = un
> value = 1
>
> path = deux
> value = 2
> ```

> [!IMPORTANT]  
> Souvent, ce n'est pas une valeur fixe que vous voudrez affecter à ce champ.  
> Il faudra alors faire appel aux fonctions Lodash.  
>
> [Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6InsgXCJpZFwiOiAxIH1cbnsgXCJpZFwiOiAyIH0iLCJzY3JpcHQiOiJbdW5wYWNrXVxuXG5bYXNzaWduXVxucGF0aCA9IGRvdWJsZVxudmFsdWUgPSBnZXQoXCJpZFwiKS5tdWx0aXBseSgyKVxuXG5bZHVtcF1cbmluZGVudCA9IHRydWUifQ==)
>
> Si on voulait créer un champ `double` contenant le double de la valeur de
> l'identifiant (ça n'a aucun sens en soi, c'est juste pour l'exemple):
>
> *Entrée*:
>
> ```jsonl
> { "id": 1 }
> { "id": 2 }
> ```
>
> *Script*:
>
> ```ini
> [unpack]
> 
> [assign]
> path = double
> value = get("id").multiply(2)
> 
> [dump]
> indent = true
> ```
>
> *Sortie*:
>
> ```json
> [{
>     "id": 1,
>     "double": 2
> },
> {
>     "id": 2,
>     "double": 4
> }]
> ```

## expand

Il peut arriver qu'on ait besoin de rassembler des objets par lots, afin
d'éviter de passer trop de fois dans une instruction (typiquement
[`URLConnect`](basics.md#url-connect), qui fait une requête à chaque objet/lot
qu'on lui passe ; sachant que plus il y a de requêtes, plus le script est lent).

C'est l'objectif de l'instruction
[`expand`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=expand).  

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6InsgXCJuYW1lXCI6IFwidW5cIiwgXCJ2YWxldXJcIjogMSB9XG57IFwibmFtZVwiOiBcImRldXhcIiwgXCJ2YWxldXJcIjogMiB9XG57IFwibmFtZVwiOiBcInRyb2lzXCIsIFwidmFsZXVyXCI6IDMgfVxueyBcIm5hbWVcIjogXCJxdWF0cmVcIiwgXCJ2YWxldXJcIjogNCB9Iiwic2NyaXB0IjoiW3VucGFja11cblxuW2V4cGFuZF1cbnNpemUgPSAyXG5wYXRoID0gdmFsZXVyXG5cbltleHBhbmQvYXNzaWduXVxucGF0aCA9IHZhbHVlXG52YWx1ZSA9IGdldChcInZhbHVlXCIpLm11bHRpcGx5KDIpXG5cbltwYWNrXSJ9)

*Entrée*:

```jsonl
{ "name": "un", "valeur": 1 }
{ "name": "deux", "valeur": 2 }
{ "name": "trois", "valeur": 3 }
{ "name": "quatre", "valeur": 4 }
```

*Script*:

```ini
[unpack]

[expand]
size = 2
path = valeur

[expand/assign]
path = value
value = get("value").multiply(2)

[pack]
```

*Sortie*:

```jsonl
{"name":"un","valeur":2}
{"name":"deux","valeur":4}
{"name":"trois","valeur":6}
{"name":"quatre","valeur":8}
```

Ici, le résultat est le même que si on n'avait pas utilisé `expand`.  

Le script aurait été:

```ini
[unpack]

[assign]
path = valeur
value = get("valeur").multiply(2)

[pack]
```

La syntaxe `expand/assign` signifie que la première instruction du sous-flux
d'`expand` est `assign`.  

Pour mieux comprendre ce qui se passe, on peut ajouter l'instruction `debug` à
deux endroits: au début du sous-flux, et à la fin (c'est-à-dire autour de la
seule instruction du sous-flux: `assign`).

> [!NOTE]  
> [`debug`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=debug) est une
> instruction qui affiche des traces dans la sortie d'erreur du script.  
> Dans Lodex, ces traces sont visibles dans le log du container de lodex.  
> Dans ezs-playground, elles apparaissent dans la partie du bas, intitulée
> `log`.  
> Par défaut, `debug` affiche l'objet courant (ainsi que son numéro d'ordre).  
> Son paramètre `text` permet de personnaliser le début du message, pour
> distinguer plusieurs instructions `debug` qui seraient présentes dans le même
> script.

Nouveau script:

```ini
[unpack]

[expand]
size = 2
path = valeur

[expand/debug]
text = <<<

[expand/assign]
path = value
value = get("value").multiply(2)

[expand/debug]
text = >>>

[pack]
```

On peut alors s'intéresser au log, et spécifiquement aux traces de niveau
`ezs:info`:

```log
ezs:info <<<#1 -> {"id":"00000000000000000001","value":1} +0ms
ezs:info >>>#1 -> {"id":"00000000000000000001","value":2} +0ms
ezs:info <<<#2 -> {"id":"00000000000000000002","value":2} +0ms
ezs:info >>>#2 -> {"id":"00000000000000000002","value":4} +0ms
ezs:info <<<#1 -> {"id":"00000000000000000003","value":3} +0ms
ezs:info >>>#1 -> {"id":"00000000000000000003","value":6} +0ms
ezs:info <<<#2 -> {"id":"00000000000000000004","value":4} +0ms
ezs:info >>>#2 -> {"id":"00000000000000000004","value":8} +0ms
```

On peut voir que les 4 objets en entrée ont été séparés en deux lots de 2 objets
(en sélectionnant uniquement les lignes avec `<<<`, en prêtant attention aux
numéros des objets: `#1` puis `#2`, deux fois de suite):

```log
ezs:info <<<#1 -> {"id":"00000000000000000001","value":1} +0ms
ezs:info <<<#2 -> {"id":"00000000000000000002","value":2} +0ms
ezs:info <<<#1 -> {"id":"00000000000000000003","value":3} +0ms
ezs:info <<<#2 -> {"id":"00000000000000000004","value":4} +0ms
```

> [!IMPORTANT]  
> Les objets envoyés au sous-flux ne sont constitués que de deux champs: `id` et
> `value`.  
> `value` contient la valeur du champ pointé par le paramètre `path` de
> l'instruction `expand` (celle du début).  
> Cela signifie bien que dans le sous-flux, on ne peut traiter qu'un seul des
> champs présents dans les objets du niveau de base: `expand` sert à *étendre*
> les valeurs d'un champ (on peut imaginer par exemple un sous-flux de
> *verbalisation* d'un identifiant).

> [!NOTE]  
> En dehors de ezs-playground, il est possible de mettre les opérations du
> sous-flux en cache (paramètre `cacheName` de `expand`).

## env

Quand on a besoin d'une valeur pour toute la durée du traitement, on peut la
mettre dans une variable d'*environnement*, en utilisant la commande
[`env`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=env).  

On l'utilise généralement au début du script, quand elle ne dépend alors pas des
éléments du flux.  
On la place alors juste après l'instruction `[use]` et ses paramètres.  

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6InsgXCJpZFwiOiAxLCBcImTpcGFydGVtZW50XCI6IDU0IH1cbnsgXCJpZFwiOiAyLCBcImTpcGFydGVtZW50XCI6IDg4IH1cbnsgXCJpZFwiOiAzLCBcImTpcGFydGVtZW50XCI6IDU1IH1cbnsgXCJpZFwiOiA0LCBcImTpcGFydGVtZW50XCI6IDU3IH0iLCJzY3JpcHQiOiJbZW52XVxucGF0aCA9IGNvbnN0YW50ZVxudmFsdWUgPSBQYXJ0b3V0IGxlIG3qbWVcblxuXG5bdW5wYWNrXVxuXG5bYXNzaWduXVxucGF0aCA9IG5vbVxudmFsdWUgPSBlbnYoXCJjb25zdGFudGVcIilcblxuW3BhY2tdIn0=):

*Entrée*:

```jsonl
{ "id": 1, "département": 54 }
{ "id": 2, "département": 88 }
{ "id": 3, "département": 55 }
{ "id": 4, "département": 57 }
```

*Script*:

```ini
[env]
path = constante
value = Partout le même

[unpack]

[assign]
path = nom
value = env("constante")

[pack]
```

*Sortie*:

```jsonl
{"id":1,"département":54,"nom":"Partout le même"}
{"id":2,"département":88,"nom":"Partout le même"}
{"id":3,"département":55,"nom":"Partout le même"}
{"id":4,"département":57,"nom":"Partout le même"}
```

Ici, on a utilisé cette variable pour ajouter une information (la même) dans
chaque élément du flux.  

> [!NOTE]  
> L'instruction `[env]` serait complètement inutile sans son pendant
> [`env()`](https://inist-cnrs.github.io/ezs/#/coding-ini?id=%c3%80-partir-d39une-variable-d39environnement),
> la fonction qu'on peut utiliser dans un paramètre `value`, comme une fonction
> Lodash.  
> Ce n'est cependant pas une fonction Lodash, elle est propre à ezs.  

> [!NOTE]  
> On peut avoir l'impression, en lisant certains scripts, que `[env]` est
> utilisée ailleurs qu'en début de script, mais c'est souvent parce qu'on est
> dans un sous-flux, comme nous allons en voir des exemples pour l'instruction
> [singleton](#singleton).  

> [!TIP]  
> Même si les valeurs attribuées aux variables d'environnement sont fixes pour
> toute la durée du flux, on peut utiliser des fonctions pour calculer ces
> valeurs.  
> Par
> [exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6InsgXCJpZFwiOiAxLCBcImTpcGFydGVtZW50XCI6IDU0IH1cbnsgXCJpZFwiOiAyLCBcImTpcGFydGVtZW50XCI6IDg4IH1cbnsgXCJpZFwiOiAzLCBcImTpcGFydGVtZW50XCI6IDU1IH1cbnsgXCJpZFwiOiA0LCBcImTpcGFydGVtZW50XCI6IDU3IH0iLCJzY3JpcHQiOiJbZW52XVxucGF0aCA9IGpvdXJcbnZhbHVlID0gZml4KEludGwuRGF0ZVRpbWVGb3JtYXQoXCJmci1GUlwiKS5mb3JtYXQobmV3IERhdGUoKSkpXG5cblt1bnBhY2tdXG5cblthc3NpZ25dXG5wYXRoID0gam91clxudmFsdWUgPSBlbnYoXCJqb3VyXCIpXG5cbltwYWNrXSJ9)
> en utilisant la date courante&nbsp;:  
>
> ```ini
> [env]
> path = jour
> value = fix(Intl.DateTimeFormat("fr-FR").format(new Date()))
> 
> [unpack]
> 
> [assign]
> path = jour
> value = env("jour")
> 
> [pack]
> ```
>
> qui, avec la même entrée, fournit la sortie suivante&nbsp;:
>
> ```jsonl
> {"id":1,"département":54,"jour":"07/02/2025"}
> {"id":2,"département":88,"jour":"07/02/2025"}
> {"id":3,"département":55,"jour":"07/02/2025"}
> {"id":4,"département":57,"jour":"07/02/2025"}
> ```

## singleton

Parfois, on a besoin d'appliquer un bout de script seulement sur le premier des
éléments du flux.  

[`singleton`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=singleton) est
là pour ça: cette instruction crée un sous-flux qui ne sera appliqué que sur le
premier élément du flux.  

Par exemple, on peut vouloir calculer une valeur à partir du premier élément du
flux, et l'utiliser dans tous les éléments du flux.  

Exemple:

*Entrée*:

```jsonl
{ "id": 1, "département": 54 }
{ "id": 2, "département": 88 }
{ "id": 3, "département": 55 }
{ "id": 4, "département": 57 }
```

*Script*:

```ini
[unpack]

[singleton]

[singleton/debug]
text = singleton

[debug]
text = principal

[pack]
```

*Log* (uniquement `ezs:info`):

```log
ezs:info singleton#1 -> {"id":1,"département":54} +0ms
ezs:info principal#1 -> {"id":1,"département":54} +0ms
ezs:info principal#2 -> {"id":2,"département":88} +0ms
ezs:info principal#3 -> {"id":3,"département":55} +0ms
ezs:info principal#4 -> {"id":4,"département":57} +0ms
```

> [!WARNING]  
> Même si on ne met pas de paramètre à l'instruction `[singleton]`, il faut la
> déclarer sur une ligne séparée avant d'écrire le sous-flux, sinon il ne
> se déclenchera pas.  

## throttle

On peut avoir besoin de *ralentir* le flux.  
Par exemple pour éviter de faire trop de requêtes sur une API, en trop peu de temps.  

[`throttle`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=throttle) est là pour ça.  
D'ailleurs son seul paramètre est `bySecond`, qui précise combien d'éléments du
flux on veut traiter *par seconde* après l'instruction.  

[Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6InsgXCJpZFwiOiAxLCBcImTpcGFydGVtZW50XCI6IDU0IH1cbnsgXCJpZFwiOiAyLCBcImTpcGFydGVtZW50XCI6IDg4IH1cbnsgXCJpZFwiOiAzLCBcImTpcGFydGVtZW50XCI6IDU1IH1cbnsgXCJpZFwiOiA0LCBcImTpcGFydGVtZW50XCI6IDU3IH1cbiIsInNjcmlwdCI6Ilt1bnBhY2tdXG5cblt0aHJvdHRsZV1cbmJ5U2Vjb25kID0gMVxuXG5bYXNzaWduXVxucGF0aCA9IHRpbWVcbnZhbHVlID0gZml4KFN0cmluZyhuZXcgRGF0ZSgpKS5zbGljZSgxNiwyNCkpXG5cbltwYWNrXSJ9):

*Entrée*:

```jsonl
{ "id": 1, "département": 54 }
{ "id": 2, "département": 88 }
{ "id": 3, "département": 55 }
{ "id": 4, "département": 57 }
```

*Script*:

```ini
[unpack]

[throttle]
bySecond = 1

[assign]
path = time
value = fix(String(new Date()).slice(16,24))

[pack]
```

*Sortie*:

```jsonl
{"id":1,"département":54,"time":"14:24:59"}
{"id":2,"département":88,"time":"14:25:00"}
{"id":3,"département":55,"time":"14:25:01"}
{"id":4,"département":57,"time":"14:25:02"}
```

Évidemment, le traitement complet sera ralenti d'autant. Pour traiter 4
éléments, le script s'exécutera en au moins 4 secondes (ici, il ne fait pas
grand chose, il est donc extrêmement rapide).  

> [!TIP]  
> Évidemment, si vous devez espacer deux actions de deux secondes, utilisez un
> paramètre `bySecond` décimal, comme `0.5` (1/**2**).  

## dedupe

On a fréquemment besoin de dédoublonner des éléments dans un flux (par exemple
des notices, en se basant sur un DOI).  
[`dedupe`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=dedupe) est là pour
ça.  

*Entrée*:

```jsonl
{ "id": 1, "département": 54 }
{ "id": 2, "département": 88 }
{ "id": 3, "département": 55 }
{ "id": 4, "département": 57 }
{ "id": 5, "département": 54 }
{ "id": 6, "département": 88 }
{ "id": 7, "département": 55 }
{ "id": 8, "département": 57 }
{ "id": 9, "département": 90 }
```

*Script*:

```ini
[unpack]

[dedupe]
path = département

[pack]
```

*Sortie*:

```log
⚠️ ERROR 👇

item #5 [dedupe] <Error: Duplicate identifier: 54 already exists>
```

> [!CAUTION]  
> Par défaut, `[dedupe]` produit une erreur, qui casse le flux, dès qu'un
> doublon est repéré.  
> C'est utile quand on veut simplement valider un corpus.  
> Par contre, pour ne garder que le premier des doublons, on peut utiliser le
> paramètre `ignore` en le mettant à `true`.  

Avec le script suivant:

```ini
[unpack]

[dedupe]
path = département
ignore = true

[pack]
```

Le résultat sera:

```jsonl
{"id":1,"département":54}
{"id":2,"département":88}
{"id":3,"département":55}
{"id":4,"département":57}
{"id":9,"département":90}
```

> [!TIP]  
> Si vous préférez garder le doublon le plus récent, tournez-vous vers
> `@ezs/analytics` et son instruction
> [`sort`](https://inist-cnrs.github.io/ezs/#/plugin-analytics?id=sort), qui
> permettra un tri inverse (avec le paramètre `reverse`) sur un champ
> chronologique, par exemple.  
>
> ```ini
> [use]
> plugin = @ezs/analytics
> 
> [unpack]
> 
> [sort]
> reverse = true
> path = id
> 
> [dedupe]
> path = département
> ignore = true
> 
> [pack]
> ```

> [!WARNING]  
> Cette instruction n'est malheureusement pas écrite pour gérer des millions
> d'éléments.  
> Pour savoir si un champ est déjà apparu dans le début du flux, elle stocke les
> valeurs dans un tableau.  
> Le risque est de manquer de mémoire, et donc de faire planter le script au
> milieu du traitement.  

## Exercices

### Quadruple

Reprenez l'exemple de [`assign`](#assign) avec le champ `double` et ajoutez un
champ `quadruple` dont la valeur sera le double de celle du champ `double` créé
juste avant.  

> [!WARNING]  
> Si vous rassemblez les deux `assign`s en un, le résultat obtenu est faux (le
> champ `quadruple` contient la même valeur que le champ `double`).  
> D'une manière générale, quand vous calculez la valeur d'un champ à partir de
> celle d'un autre (surtout quand vous venez de le créer), ne le faites pas en
> une étape (ici, dans le même `assign`) car le résultat ne sera pas garanti.  

<details>
<summary>
Voir la solution
</summary>

[Script](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6InsgXCJpZFwiOiAxIH1cbnsgXCJpZFwiOiAyIH0iLCJzY3JpcHQiOiJbdW5wYWNrXVxuXG5bYXNzaWduXVxucGF0aCA9IGRvdWJsZVxudmFsdWUgPSBnZXQoXCJpZFwiKS5tdWx0aXBseSgyKVxuXG5bYXNzaWduXVxucGF0aCA9IHF1YWRydXBsZVxudmFsdWUgPSBnZXQoXCJkb3VibGVcIikubXVsdGlwbHkoMilcblxuW2R1bXBdXG5pbmRlbnQgPSB0cnVlIn0=):

```ini
[unpack]

[assign]
path = double
value = get("id").multiply(2)

[assign]
path = quadruple
value = get("double").multiply(2)

[dump]
indent = true
```

</details>

### Départements

Et si nous verbalisions des numéros de département ?

*Entrée*:

```jsonl
{ "id": 1, "département": 54 }
{ "id": 2, "département": 88 }
{ "id": 3, "département": 55 }
{ "id": 4, "département": 57 }
```

On peut utiliser `env` pour stocker d'abord un objet faisant correspondre un
numéro de département et son nom:

```json
{ "54": "Meurthe-et-Moselle", "55": "Meuse", "57": "Moselle", "88": "Vosges" }
```

`expand` est l'instruction idéale pour *étendre* le numéro de département à son
nom.

> [!TIP]  
> Pour mettre à jour la valeur d'un champ `value`, on peut utiliser la fonction
> Lodash `update("value", fn).get("value)`.  
> Ici, `fn` est la définition d'une fonction ayant pour paramètre la valeur du
> champ `value`.  
>
> Une alternative serait `get("value").thru(fn)`, qui a l'avantage d'être plus
> courte, mais moins parlante (`thru` applique `fn` au résultat de la fonction
> précédente, c'est le couteau suisse de Lodash).  

> [!TIP]  
> En JavaScript, on a deux syntaxes pour définir une fonction anonyme&nbsp;:
>
> 1. `function (paramètre) { return résultat; }`
> 2. `paramètre => résultat`

> [!TIP]  
> Pour ceux qui ne s'en rappelleraient pas (ou ne l'auraient jamais su), en
> JavaScript, on peut récupérer la valeur d'un champ d'un objet (comme celui qui
> contient les noms des départements) en utilisant la syntaxe
> `nomObjet["nomDuChamp"]` (même si on peut aussi utiliser
> `nomObjet.nomDuChamp`, mais celle-ci n'est pas adaptée à notre cas, car
> `nomDuChamp` sera une variable).

<details>
<summary>
Voir la solution
</summary>

Avec `update` :

```ini
[env]
path = noms
value = fix({ "54": "Meurthe-et-Moselle", "55": "Meuse", "57": "Moselle", "88": "Vosges" })

[unpack]

[expand]
size = 2
path = département

[expand/assign]
path = value
value = update("value", dept => env("noms")[dept]).get("value")

[pack]
```

Avec `thru` :

```ini
[env]
path = noms
value = fix({ "54": "Meurthe-et-Moselle", "55": "Meuse", "57": "Moselle", "88": "Vosges" })

[unpack]

[expand]
size = 2
path = département

[expand/assign]
path = value
value = get("value").thru(dept => env("noms")[dept])

[pack]
```

</details>

### Identifiant de corpus

Faire un script qui donne un nom de corpus à tous les éléments du flux, à partir
du *timestamp* de passage du premier élément du flux.

Ce script ne convient pas&nbsp;:

```ini
[unpack]

[assign]
path = corpus
value = fix(Date.now())

[pack]
```

Avec un script utilisant `Date.now()` pour récupérer le *timestamp* à chaque
élément, comme le *timestamp* est précis à la milliseconde, on risque d'avoir
des éléments d'un même flux avec un identifiant de flux différent. C'est
d'autant plus probable que le nombre d'éléments du flux est grand (et long à
traiter).  

<details>
<summary>
Voir la solution
</summary>

L'instruction `[env]` est adaptée&nbsp;: elle calcule une valeur une seule fois
pour tout le flux.  

```ini
[env]
path = corpus
value = fix(Date.now())

[unpack]

[assign]
path = corpus
value = env("corpus")

[pack]
```

> [!NOTE]  
> Si on voulait être plus royaliste que le roi, on pourrait faire remarquer que
> `[env]` est exécutée *avant* que le premier élément du flux soit traité.  
> C'est l'objet de l'exercice suivant.  

</details>

### Identifiant de corpus (2)

Faire un script qui donne un nom de corpus à tous les éléments du flux, à partir
de la valeur du champ `département` du premier élément du flux.

Exemple d'entrée:

```jsonl
{ "id": 1, "département": 54 }
{ "id": 2, "département": 88 }
{ "id": 3, "département": 55 }
{ "id": 4, "département": 57 }
```

> [!IMPORTANT]  
> Cette fois, l'utilisation de `[env]` ne sera pas suffisante, puisqu'elle est
> exécutée *au début* du flux (avant le premier élément).  
> Il va donc falloir se tourner vers l'instruction [`[singleton]`](#singleton).  

> [!IMPORTANT]  
> L'utilisation de `[singleton]` n'est pas suffisante, il faut l'utiliser pour
> récupérer (`get()`) la valeur du champ `département` du premier élément en
> combinaison avec `[env]` pour affecter une variable d'environnement qu'on
> pourra récupérer dans le flux normal.  

<details>
<summary>
Voir la solution
</summary>

```ini
[unpack]

[singleton]
[singleton/env]
path = corpus
value = get("département")

[assign]
path = corpus
value = env("corpus")

[pack]
```

Et le résultat devrait être:

```jsonl
{"id":1,"département":54,"corpus":54}
{"id":2,"département":88,"corpus":54}
{"id":3,"département":55,"corpus":54}
{"id":4,"département":57,"corpus":54}
```

</details>

### Attendre 4 secondes

Cela n'a aucun intérêt autre que pédagogique, mais essayons de faire un script
qui attend 4 secondes avant de rendre la main.  

Il faut lui fournir un flux d'entrée, quel qu'il soit. Mais le plus court est le
mieux, si on ne veut pas attendre plus que 4 secondes. Un élément est l'idéal.  

> [!TIP]  
> Pour vérifier le temps mis par le script, utilisez les logs, dans la partie
> basse de la fenêtre.  

<details>
<summary>
Voir la solution
</summary>

*Entrée* (mais l'élément peut être différent, ça n'a aucune importance):

```jsonl
{ "id": 1, "département": 54 }
```

*Script*:

```ini
[unpack]

[throttle]
bySecond = 0.25

[pack]
```

La sortie est identique à l'entrée, mais on ne s'en soucie pas.

*Log*:

```log
ezs:trace 0.0052s cumulative 0.0002s elapsed for [unpack] +0ms
ezs:trace 4.0045s cumulative 4.0004s elapsed for [throttle] +0ms
```

Attention! Si votre flux d'entrée a plus qu'un élément, le temps d'exécution
sera multiplié par le nombre d'éléments.  

</details>
