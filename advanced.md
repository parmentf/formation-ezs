# Usages avancés

N'hésitez pas à explorer [la documentation
d'ezs](https://inist-cnrs.github.io/ezs/#/?id=ezs-illustration), et les
instructions disponibles dans ces deux *plugins*, car nous n'avons fait que
survoler les instructions des deux *plugins* les plus utilisés: `@ezs/core` (41
instructions) et `@ezs/basics` (32 instructions).  

> [!NOTE]  
> Nous sommes preneurs de toute amélioration de cette documentation.  
> Nous savons qu'elle est largement perfectible.  

## plugins

Mais il y a bien d'autres instructions disponibles dans les *plugins* déjà
publiés, même s'ils sont plus spécialisés.  

- [@ezs/analytics](https://inist-cnrs.github.io/ezs/#/plugin-analytics):
  croiser, compter, trier, agréger des flux (31 instructions),
- [@ezs/ark](https://inist-cnrs.github.io/ezs/#/plugin-ark): générer des
  [identifiants ARK](https://fr.wikipedia.org/wiki/Archival_Resource_Key) (même
  si, à l'Inist, nous avons un service de création d'ARK unique pour s'assurer
  qu'aucun identifiant créé n'existe déjà; 1 instruction),
- [@ezs/conditor](https://inist-cnrs.github.io/ezs/#/plugin-conditor): requêter
  l'API Conditor, le WoS, et trouver un identifiant RNSR à partir d'une adresse
  de laboratoire (8 instructions),
- [@ezs/istex](https://inist-cnrs.github.io/ezs/#/plugin-istex): utiliser l'API
  ISTEX, et convertir des notices en triplets
  [turtle](https://fr.wikipedia.org/wiki/Turtle_(syntaxe)) (13 instructions),
- [@ezs/libpostal](https://inist-cnrs.github.io/ezs/#/plugin-libpostal):
  utiliser [libpostal](https://github.com/openvenues/libpostal), une
  bibliothèque C qui analyse/normalise les adresses postales du monde entier (4
  instructions),
- [@ezs/loterre](https://inist-cnrs.github.io/ezs/#/plugin-loterre): interagir
  avec [Loterre](https://loterre.istex.fr/fr/) (4 instructions),
- [@ezs/sparql](https://inist-cnrs.github.io/ezs/#/plugin-sparql): interroger un
  SPARQL endpoint (3 instructions),
- [@ezs/spawn](https://inist-cnrs.github.io/ezs/#/plugin-spawn): utiliser des
  scripts, ou des programmes écrits dans d'autres langages de programmation (1
  instruction),
- [@ezs/storage](https://inist-cnrs.github.io/ezs/#/plugin-storage): stocker et
  récupérer des flux (3 instructions),
- [@ezs/strings](https://inist-cnrs.github.io/ezs/#/plugin-strings): manipuler
  des chaînes de caractères (4 instructions),
- [@ezs/teeft](https://inist-cnrs.github.io/ezs/#/plugin-teeft): extraire des
  mots-clés spécifiques à un texte (en français ou en anglais; 17 instructions),
- [@ezs/transformers](https://inist-cnrs.github.io/ezs/#/plugin-transformers):
  les
  [*transformers*](https://www.lodex.fr/docs/documentation/principales-fonctionnalites-disponibles/transformer-des-donnees/)
  (31 instructions) de lodex,
- [@ezs/xslt](https://inist-cnrs.github.io/ezs/#/plugin-xslt): transformer du
  XML via XSLT (2 instructions).

## Scripts complexes

Les scripts en `.ini` peuvent être très complexes, et utiliser des commandes
avec des sous-flux (comme [expand](core.md#expand)).  
Ils peuvent devenir très long.  
Une stratégie commune pour faciliter leur lecture (et donc leur maintenabilité)
est de les séparer en plusieurs fichiers.  

Ainsi, on peut séparer le script initial `departments.ini`:

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

en deux scripts `long.ini` et `replace-dept.ini`:

```ini
# Fichier long.ini
[unpack]

[expand]
size = 2
path = département

[expand/assign]
file = replace-dept.ini

# Ici, on suppose que d'autres traitements ont lieu
# ...

[pack]
```

```ini
# Fichier replace-dept.ini
[env]
path = noms
value = fix({ "54": "Meurthe-et-Moselle", "55": "Meuse", "57": "Moselle", "88": "Vosges" })

# Ici, pas besoin d'analyse du flux, ce n'est pas du texte qui arrive,
# ce sont directement des objets javascript.
# Donc, pas de [unpack]

[assign]
path = value
value = update("value", dept => env("noms")[dept]).get("value")

# Là, il ne faut pas convertir les objets javascript en texte, 
# c'est le script appelant qui le fera, à la toute fin du traitement.
# Donc, pas de [pack]
```

Toutes les instructions pouvant utiliser des sous-flux (comme `[expand]`) ont un
paramètre `file` qui agit comme une syntaxe alternative à celle que nous avons
vue plus haut.

> [!NOTE]  
> Dans le contexte des services web, on trouve des scripts ezs avec l'extension `.cfg`.  
> La raison est que `ezs` peut fonctionner comme un serveur de services web, et
> qu'il génère automatiquement une liste des *routes* dans la description
> [OpenApi](https://www.openapis.org/) du serveur.  
> Pour ce faire, il se contente de parcourir ses fichiers au démarrage, et de
> recenser les chemins des fichiers en `.ini`.  
>
> Exemple: <https://base-line.services.istex.fr/> permet la création de [cette
> documentation](https://openapi.services.istex.fr/?urls.primaryName=base-line%20-%20Services%20d%C3%A9di%C3%A9s%20aux%20tests).
>
> Mais quand certains de ces scripts font parties de scripts complexes, le
> comportement souhaité n'est pas de lister *tous* les scripts ezs, mais
> seulement ceux qu'on peut lancer (comme `long.ini` dans l'exemple précédent),
> et donc on utilise l'extension `.cfg` pour tous les sous-flux de ces scripts.  
> Les fichiers en `.cfg` ont l'avantage d'être colorisés comme les `.ini` dans
> les éditeurs de développement.
>
> C'est ainsi que sont écrits certains de nos web services.  
> Exemple:
> [`v1/lda.ini`](https://github.com/Inist-CNRS/web-services/blob/1aa75018825a14626ebcaf048419d99675b5f15c/services/data-computer/v1/lda.ini#L37-L68)

```ini
; fichier v1/lda.ini
[env]
; Cette variable d'environnement est définie dans le flux et tous ses sous-flux
path = generator
value = lda

[use]
; Les plugins utilisés sont disponibles aussi dans les sous-flux
; (même s'il est préférable de les redéclarer, au cas où un autre script voulait 
; réutiliser le même sous-script)
plugin = basics
plugin = analytics
plugin = spawn

# Step 1 (générique): Charger le fichier corpus
[delegate]
; delegate est une instruction qui ne fait qu'appeler un sous-flux sur tous les
; éléments du flux.
file = charger.cfg

# Step 2 (générique): Traiter de manière asynchrone les items reçus
[fork]
standalone = true
logger = logger.cfg

# Step 2.1 (spécifique): Lancer un calcul sur tous les items reçus
[fork/exec]
# command should be executable !
command = ./v1/lda.py
; Ici, le programme principal est écrit en Python.
; L'avantage c'est qu'il n'a pas à gérer le flux (problème complexe dans la 
; plupart des langages de programmation)
args = fix('-p')
args = env('nbTopic',6)

# Step 2.2 (générique): Enregistrer le résultat et signaler que le traitement est fini
[fork/delegate]
file = recorder.cfg

# Step 3 : Renvoyer immédiatement un seul élément indiquant comment récupérer le résultat
#          quand il sera prêt
[delegate]
file = recipient.cfg
```

## loaders

Un [*loader* de
Lodex](https://www.lodex.fr/docs/documentation/principales-fonctionnalites-disponibles/les-loaders/)
est un script ezs destiné à convertir un fichier de données d'un format donné en
un flux d'objets JavaScript à charger dans la base de données Mongo de Lodex.  

On peut fournir soi-même un *loader*, dans le cas où aucun des [*loaders*
fournis](https://github.com/Inist-CNRS/lodex/tree/master/workers/loaders) ne
nous satisfait.  
Le plus simple est tout de même de partir d'un script existant.  

### [`json-lines.ini`](https://github.com/Inist-CNRS/lodex/blob/89bbfef8a07e2417dda88d71d3b2ef5316c87290/workers/loaders/json-lines.ini)

```ini
append = pack
label = json-lines

# load some plugins to activate some statements
[use]
plugin = basics

[unpack]

# Ensures that each object contains an identification key (required by lodex)
[swing]
test = pick(['URI', 'uri']).pickBy(_.identity).isEmpty()
[swing/identify]

# Ignore objects with duplicate URI
[dedupe]
ignore = true

# Prevent keys form containing dot path notation (which is forbidden by nodejs mongoDB driver)
[OBJFlatten]
separator = fix('.')
reverse = true

# Uncomment to see each data sent to the database
#[debug]

# Add contextual metadata related to the import
[assign]
path = lodexStamp.importedDate
value = fix(new Date()).thru(d => d.toDateString())
path = lodexStamp.usedParser
value = env('parser')
path = lodexStamp.uploadedFilename
value = env('source')
path = uri
value = get('uri').trim()
```

> [!TIP]  
> La première ligne (`append = pack`) est là pour aider au développement du *loader*.  
> En effet, ce script est lancé dans l'environnement de lodex, il est destiné à
> renvoyer des objets JavaScript directement.  
> Donc contrairement à ce que nous avons fait dans le *playground*, il n'y a pas
> besoin d'une instruction finale qui transforme les objets javascript en chaîne
> de caractères.  
> Mais quand on développe le *loader*, on est en dehors de lodex, et on veut
> pouvoir voir le résultat du script (autrement que par `[object Object]`).  
> C'est pourquoi ezs accepte une syntaxe permettant d'exécuter dans lodex (ou
> dans un programme JavaScript) un script qui fournit des objets, et qui, sans
> modification, fournit une chaîne de caractères quand on le lance via la ligne
> de commande.  
>
> Le script se comporte alors comme si sa dernière instruction (*append* =
> ajouter à la fin) était `pack`.  

> [!WARNING]  
> La syntaxe `append = pack` ne fonctionne pas dans le *playground* (c'est une
> métadonnée ignorée par ezs).  
> Aussi, si vous utilisez le *playground* pour développer un *loader*, n'oubliez
> pas supprimer ou de commenter la ligne `[pack]` avant de l'utiliser dans
> lodex.  

> [!NOTE]  
> La ligne `label = json-lines` permet de lier *loader* au label à afficher dans
> la liste des *loaders*, en utilisant le [fichier de
> traductions](https://github.com/Inist-CNRS/lodex/blob/89bbfef8a07e2417dda88d71d3b2ef5316c87290/src/app/custom/translations.tsv#L482).  

> [!NOTE]  
> La dernière instruction ajoute une colonne `lodexStamp` à chaque élément du
> flux, afin d'avoir une trace&nbsp;:  
>
> - de la date d'importation `importedDate`
> - du nom du *loader* utilisé `parser`
> - du nom du fichier importé `uploadedFilename`
> - de l'identifiant du document `uri`

> [!IMPORTANT]  
> Il y a une contrainte imposée par lodex: la colonne `uri` doit exister (et
> contenir un identifiant).  
>
> C'est l'objet de cette partie du *loader*:
>
> ```ini
> # Ensures that each object contains an identification key (required by lodex)
> [swing]
> test = pick(['URI', 'uri']).pickBy(_.identity).isEmpty()
> [swing/identify]
> ```
>
> L'instruction
> [`[swing]`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=swing) applique
> un sous-script (ici uniquement composé de l'instruction `[identify]`) aux
> éléments du flux, quand l'élément vérifie le test (ici, le sous-flux est
> déclenché quand l'élément courant ne possède pas de champ `uri` ou `URI`).  
> L'instruction
> [`[identify]`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=identify) se
> contente d'ajouter un champ `uri` avec un identifiant unique.  

<!-- ### [`json-optimzed.ini`](https://github.com/Inist-CNRS/lodex/blob/89bbfef8a07e2417dda88d71d3b2ef5316c87290/workers/loaders/json-optimized.ini)

Le *loader* `json-optimized` a une structure très similaire à celle de
`json-lines`, mais utilise une instruction supplémentaire&nbsp;:
[`[OBJStandardize]`](https://inist-cnrs.github.io/ezs/#/plugin-basics?id=objstandardize).

Cette instruction s'assure que toutes les colonnes du *dataset* auront une
valeur (une chaîne vide par défaut). -->

### plugin lodex

[`json-report.ini`](https://github.com/Inist-CNRS/lodex/blob/fc6fa35dbce83a3178df0989835266f9adee81c1/workers/loaders/json-report.ini)
utilise un *plugin* qui n'est pas listé dans la documentation d'ezs: `lodex`.  

Comme ce *plugin* contient des instructions propres à lodex (et qui ne sont
utiles que dans ce contexte), il a été décidé de localiser le code source de ce
*plugin* dans [le dépôt de
lodex](https://github.com/Inist-CNRS/lodex/tree/fc6fa35dbce83a3178df0989835266f9adee81c1/packages/ezsLodex).  

On y trouve des instructions traitant de formats du web sémantique
(NQuads,JsonLd, Turtle), et d'autres instructions utilisées principalement dans
les routines (on y trouve des scripts combinant au moins le *plugin* `lodex` et
le *plugin* `analytics`).  

Dans le cas de `json-report`, on utilise l'instruction `objects2columns` qui
aplatit les éléments du flux pour en faire des éléments tabulaires (c'est-à-dire
à un seul niveau).  

Par exemple, un élément de départ

```json
[{
  "racine": {
    "branche": "feuille"
  },
  "logiciel": "lodex",
  "version": 15
}]
```

sera transformé en un objet plus simple:

```json
[{
  "racine": "{\"branche\":\"feuille\"}",
  "logiciel": "lodex",
  "version": 15
}]
```

> [!TIP]  
> Pour convertir une structure complexe à plusieurs niveaux en une série de
> colonnes, on utilise plutôt
> [`[OBJFlatten]`](https://inist-cnrs.github.io/ezs/#/plugin-basics?id=objflatten)
> qui transforme le chemin jusqu'à la feuille en nom de colonne.  
>
> Avec le même exemple que tout-à-l'heure, on obtiendra (avec les paramètres par
> défaut)&nbsp;:  
>
> ```json
> [{
>     "racine/branche": "feuille",
>     "logiciel": "lodex",
>     "version": 15
> }]
> ```

> [!NOTE]  
> On voit dans `json-report.ini` une utilisation de `[OBJECTFlatten]` qui était
> déjà apparue dans d'autres *loaders*&nbsp;:  
>
> ```ini
> # Prevent keys from containing dot path notation (which is forbidden by nodejs mongoDB driver)
> [OBJFlatten]
> separator = fix('.')
> reverse = true
> safe = true
> ```
>
> À cause du paramètre `reverse = true`, cette instruction fait l'inverse&nbsp;:
> elle recrée une structure arborescente quand elle rencontre un nom de champ
> avec une notation pointée.  
>
> [Exemple](http://ezs-playground.daf.intra.inist.fr/?x=eyJpbnB1dCI6Ilt7XG4gIFwicmFjaW5lLmJyYW5jaGVcIjogXCJmZXVpbGxlXCIsXG4gIFwibG9naWNpZWxcIjogXCJsb2RleFwiLFxuICBcInZlcnNpb25cIjogMTVcbn1dXG4iLCJzY3JpcHQiOiJbSlNPTlBhcnNlXVxuXG5bT0JKRmxhdHRlbl1cbnNlcGFyYXRvciA9IGZpeChcIi5cIilcbnJldmVyc2UgPSB0cnVlXG5zYWZlID0gdHJ1ZVxuXG5bZHVtcF1cbmluZGVudCA9IHRydWUifQ==)&nbsp;:
>
> *Entrée*:
>
> ```json
> [{
>   "racine.branche": "feuille",
>   "logiciel": "lodex",
>   "version": 15
> }]
> ```
>
> *Script*:
>
> ```ini
> [JSONParse]
> 
> [OBJFlatten]
> separator = fix(".")
> reverse = true
> safe = true
> 
> [dump]
> indent = true
> ```
>
> *Sortie*:
>
> ```json
> [{
>     "racine": {
>         "branche": "feuille"
>     },
>     "logiciel": "lodex",
>     "version": 15
> }]
> ```
>
> L'intérêt de cette instruction est de s'assurer qu'aucun nom de colonne ne
> contient de `.`, car c'est incompatible avec l'utilisation de MongoDB (la base
> de données de lodex).  
>
> Dans le cas présent, la colonne `racine` contiendrait un objet javascript (ce
> qui est tout-à-fait utilisable).  

### *loader* spécialisé

Le *loader*
[`query-conditor-for-halcnrs.ini`](https://github.com/Inist-CNRS/lodex/blob/fc6fa35dbce83a3178df0989835266f9adee81c1/workers/loaders/query-conditor-for-halcnrs.ini)
est spécialisé dans l'interrogation d'une base bibliographique (conditor), et ne
garde que les notices venant de HAL.  

La première partie convertit le fichier importé en requête sur l'API
[CorHAL](https://corhal-api.inist.fr/api-docs/), et s'assure que chaque notice
retournée aura un champ `uri` unique&nbsp;:  

```ini
[TXTConcat]

[replace]
path = q
value = self().trim()

[CORHALFetch]
url = https://corhal-api.inist.fr
retries = 3
timeout = 60000

[assign]
path = uri
value = get('business.sourceUidChain')
```

Ensuite vient la sélection et la transformation des champs (partie la plus
longue du script).  

Notez que la dernière instruction est `[exchange]` qui, combinée à la fonction
lodash `omit()`, permet de se débarrasser des champs non souhaités ou inutiles.  

[`[exchange]`](https://inist-cnrs.github.io/ezs/#/plugin-core?id=exchange)
permet de remplacer tout un élément par un autre (il ne faut pas se laisser abuser
par le paramètre `value` qui pourrait laisser croire qu'on ne remplace qu'un
champ `value`: c'est toute la valeur de l'élément qui est remplacée).  

<!-- Les plus simples:

- csv.ini
- json-lines.ini

intéressants:

- json-optimized.ini (OBJStandardize)
- append = (exécuté en dehors de lodex)
- assign des métadonnées (env("parser"), env("source"))
- json-report.ini (plugin lodex,
  [objects2columns](https://github.com/Inist-CNRS/lodex/tree/master/packages/ezsLodex#objects2columns))
- query-conditor.ini
- query-istex.ini
- tei-persee.ini (sélection de champs / tei.ini)
- test-country.ini (expand / loterre) ==> exercice: utiliser le vrai service loterre

complexes:

- json-protege.ini
- query-conditor-for-halcnrs.ini
- query-openalex.ini
- skos.ini (overturn/swing) -->

## le cas Lodash

<!-- 
Voir `query-conditor-for-halcnrs.ini` 
-->
