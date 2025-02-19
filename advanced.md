# Usages avancés

N'hésitez pas à explorer [la documentation
d'ezs](https://inist-cnrs.github.io/ezs/#/?id=ezs-illustration), et les
instructions disponibles dans ces deux *plugins*, car nous n'avons fait que
survoler les instructions des deux *plugins* les plus utilisés: `@ezs/core` et
`@ezs/basics`.  

> [!NOTE]  
> Nous sommes preneurs de toute amélioration de cette documentation.  
> Nous savons qu'elle est largement perfectible.  

## plugins

Mais il y a bien d'autres instructions disponibles dans les *plugins* déjà
publiés, même s'ils sont plus spécialisés.  

- [@ezs/analytics](https://inist-cnrs.github.io/ezs/#/plugin-analytics):
  croiser, compter, trier, agréger des flux,
- [@ezs/ark](https://inist-cnrs.github.io/ezs/#/plugin-ark): générer des
  [identifiants ARK](https://fr.wikipedia.org/wiki/Archival_Resource_Key) (même
  si, à l'Inist, nous avons un service de création d'ARK unique pour s'assurer
  qu'aucun identifiant créé n'existe déjà),
- [@ezs/conditor](https://inist-cnrs.github.io/ezs/#/plugin-conditor): requêter
  l'API Conditor, le WoS, et trouver un identifiant RNSR à partir d'une adresse
  de laboratoire,
- [@ezs/istex](https://inist-cnrs.github.io/ezs/#/plugin-istex): utiliser l'API
  ISTEX, et convertir des notices en triplets
  [turtle](https://fr.wikipedia.org/wiki/Turtle_(syntaxe)),
- [@ezs/libpostal](https://inist-cnrs.github.io/ezs/#/plugin-libpostal):
  utiliser [libpostal](https://github.com/openvenues/libpostal), une
  bibliothèque C qui analyse/normalise les adresses postales du monde entier,
- [@ezs/loterre](https://inist-cnrs.github.io/ezs/#/plugin-loterre): interagir
  avec [Loterre](https://loterre.istex.fr/fr/),
- [@ezs/sparql](https://inist-cnrs.github.io/ezs/#/plugin-sparql): interroger un
  SPARQL endpoint,
- [@ezs/spawn](https://inist-cnrs.github.io/ezs/#/plugin-spawn): utiliser des
  scripts, ou des programmes écrits dans d'autres langages de programmation,
- [@ezs/storage](https://inist-cnrs.github.io/ezs/#/plugin-storage): stocker et
  récupérer des flux,
- [@ezs/strings](https://inist-cnrs.github.io/ezs/#/plugin-strings): manipuler
  des chaînes de caractères,
- [@ezs/teeft](https://inist-cnrs.github.io/ezs/#/plugin-teeft): extraire des
  mots-clés spécifiques à un texte (en français ou en anglais),
- [@ezs/transformers](https://inist-cnrs.github.io/ezs/#/plugin-transformers):
  les
  [*transformers*](https://www.lodex.fr/docs/documentation/principales-fonctionnalites-disponibles/transformer-des-donnees/)
  de lodex,
- [@ezs/xslt](https://inist-cnrs.github.io/ezs/#/plugin-xslt): transformer du
  XML via XSLT.

## `.cfg` (delegate ; ex: loterre-resolvers)

## loaders

## le cas Lodash
