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

> [!WARNING]  
> Si vous avez rassemblé les deux `assign`s en un, le résultat obtenu est faux
> (le champ `quadruple` contient la même valeur que le champ `double`).  
> D'une manière générale, quand vous calculez la valeur d'un champ à partir de
> celle d'un autre (surtout quand vous venez de le créer), ne le faites pas en
> une étape (ici, dans le même `assign`) car le résultat ne sera pas garanti.  

</details>
