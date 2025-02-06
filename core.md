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
> path = un
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
path = value
value = get("value").multiply(2)

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

## combine

## singleton

## env

## throttle

## dedupe

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
{ 54: "Meurthe-et-Moselle", 55: "Meuse", 57: "Moselle", 88: "Vosges" }
```

`expand` est l'instruction idéale pour *étendre* le numéro de département à son
nom.

<details>
<summary>
Voir la solution
</summary>

```ini
[env]
path = noms
value = fix({ 54: "Meurthe-et-Moselle", 55: "Meuse", 57: "Moselle", 88: "Vosges" })

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
