<properties
    pageTitle="Ajouter le connecteur Bing Search à PowerApps ou à des applications logiques | Microsoft Azure"
    description="Vue d’ensemble du connecteur Bing Search avec les paramètres de l’API REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="05/18/2016"
   ms.author="mandia"/>

# Prise en main du connecteur Bing Search 
Connecte-vous à Bing Search pour rechercher des actualités, des vidéos et bien plus encore. Le connecteur Bing Search peut être utilisé dans :

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-bingsearch.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-bingsearch.md)


Avec Bing Search, vous pouvez effectuer les opérations suivantes :

- Créer votre flux d’activité en fonction des données que vous obtenez pendant votre recherche. 
- Utiliser des actions pour rechercher des images, des actualités et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, vous pouvez rechercher une vidéo, puis utiliser Twitter pour la publier dans un flux Twitter.
- Ajouter le connecteur Bing Search à PowerApps Enterprise. Vos utilisateurs peuvent ensuite utiliser ce connecteur dans leurs applications. 

Pour plus d’informations sur l’ajout d’un connecteur à PowerApps Enterprise, consultez [Register connector in PowerApps](../power-apps/powerapps-register-from-available-apis.md) (Inscrire un connecteur dans PowerApps).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
Bing Search inclut les actions suivantes. Il n’y a aucun déclencheur.

Déclencheurs | Actions
--- | ---
Aucun | <ul><li>Rechercher sur le web</li><li>Rechercher des vidéos</li><li>Rechercher des images</li><li>Rechercher des actualités</li><li>Recherche liée</li><li>Rechercher l’orthographe de mots</li><li>Rechercher tout</li></ul>

Tous les connecteurs prennent en charge les données aux formats JSON et XML.


## Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### Rechercher sur le web 
Récupère des sites web à partir d’une recherche Bing. ```GET: /Web```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|(aucun) |Texte à rechercher (exemple : « xbox »)|
|maxResult|integer|no|query|(aucun) |Nombre maximal de résultats à renvoyer|
|startOffset|integer|no|query| (aucun)|Nombre de résultats à ignorer|
|adultContent|string|no|query|(aucun) |Filtrage de contenu pour adultes. Valeurs valides : <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(aucun) |Marché ou région pour affiner la recherche (exemple : fr-FR)|
|longitude|number|no|query| (aucun)|Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450)|
|latitude|number|no|query| (aucun)|Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696)|
|webFileType|string|no|query|(aucun) |Type de fichier pour affiner la recherche (exemple : « DOC »)|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Rechercher ded vidéos 
Récupère des vidéos à partir d’une recherche Bing. ```GET: /Video```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|(aucun) |Texte à rechercher (exemple : « xbox »)|
|maxResult|integer|no|query| (aucun)|Nombre maximal de résultats à renvoyer|
|startOffset|integer|no|query|(aucun) |Nombre de résultats à ignorer|
|adultContent|string|no|query|(aucun) |Filtrage de contenu pour adultes. Valeurs valides : <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(aucun) |Marché ou région pour affiner la recherche (exemple : fr-FR)|
|longitude|number|no|query|(aucun) |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450)|
|latitude|number|no|query|(aucun) |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696)|
|videoFilters|string|no|query|(aucun) |Recherche en fonction de la taille, de l’aspect, de la couleur, du style, du visage ou de toute combinaison de ces critères. Valeurs valides : <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Par exemple : « Duration:Short+Resolution:High »|
|videoSortBy|string|no|query|(aucun) |Ordre de tri des résultats. Valeurs valides : <ul><li>Date</li><li>Relevance</li></ul> <p>L’ordre de tri en fonction de la date est décroissant.</p>|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Rechercher des images    
Récupère des images à partir d’une recherche Bing. ```GET: /Image```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|(aucun) |Texte à rechercher (exemple : « xbox »)|
|maxResult|integer|no|query|(aucun) |Nombre maximal de résultats à renvoyer|
|startOffset|integer|no|query|(aucun) |Nombre de résultats à ignorer|
|adultContent|string|no|query|(aucun) |Filtrage de contenu pour adultes. Valeurs valides : <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(aucun) |Marché ou région pour affiner la recherche (exemple : fr-FR)|
|longitude|number|no|query| (aucun)|Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450)|
|latitude|number|no|query|(aucun) |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696)|
|imageFilters|string|no|query|(aucun) |Recherche en fonction de la taille, de l’aspect, de la couleur, du style, du visage ou de toute combinaison de ces critères. Valeurs valides : <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Par exemple : « Size:Small+Aspect:Square »|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Rechercher des actualités    
Récupère des résultats d’actualités à partir d’une recherche Bing. ```GET: /News```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|(aucun) |Texte à rechercher (exemple : « xbox »)|
|maxResult|integer|no|query|(aucun) |Nombre maximal de résultats à renvoyer|
|startOffset|integer|no|query| (aucun)|Nombre de résultats à ignorer|
|adultContent|string|no|query|(aucun) |Filtrage de contenu pour adultes. Valeurs valides : <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(aucun) |Marché ou région pour affiner la recherche (exemple : fr-FR)|
|longitude|number|no|query|(aucun) |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450)|
|latitude|number|no|query|(aucun) |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696)|
|newsSortBy|string|no|query| (aucun)|Ordre de tri des résultats. Valeurs valides : <ul><li>Date</li><li>Relevance</li></ul> <p>L’ordre de tri en fonction de la date est décroissant.</p>|
|newsCategory|string|no|query| |Catégorie d’actualités pour affiner la recherche (exemple : « rt\_Business »)|
|newsLocationOverride|string|no|query|(aucun) |Remplacement pour la détection du lieu par Bing. Ce paramètre s’applique uniquement au marché fr-FR. Le format d’entrée est « US ».<state /> (Exemple : « US.WA »)|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Rechercher l’orthographe de mots    
Récupère des suggestions orthographiques. ```GET: /SpellingSuggestions```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query| (aucun)|Texte à rechercher (exemple : « xbox »)|
|maxResult|integer|no|query|(aucun) |Nombre maximal de résultats à renvoyer|
|startOffset|integer|no|query| (aucun)|Nombre de résultats à ignorer|
|adultContent|string|no|query|(aucun) |Filtrage de contenu pour adultes. Valeurs valides : <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query| (aucun)|Marché ou région pour affiner la recherche (exemple : fr-FR)|
|longitude|number|no|query|(aucun) |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450)|
|latitude|number|no|query|(aucun) |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696)|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Recherche liée    
Récupère des résultats de recherche liée à partir d’une recherche Bing. ```GET: /RelatedSearch```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|(aucun) |Texte à rechercher (exemple : « xbox »)|
|maxResult|integer|no|query|(aucun) |Nombre maximal de résultats à renvoyer|
|startOffset|integer|no|query| (aucun)|Nombre de résultats à ignorer|
|adultContent|string|no|query|(aucun) |Filtrage de contenu pour adultes. Valeurs valides : <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(aucun) |Marché ou région pour affiner la recherche (exemple : fr-FR)|
|longitude|number|no|query|(aucun) |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450)|
|latitude|number|no|query| (aucun)|Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696)|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


### Rechercher tout    
Récupère la totalité des sites web, vidéos, images, etc. à partir d’une recherche Bing. ```GET: /CompositeSearch```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|query|string|yes|query|(aucun) |Texte à rechercher (exemple : « xbox »)|
|maxResult|integer|no|query|(aucun) |Nombre maximal de résultats à renvoyer|
|startOffset|integer|no|query|(aucun) |Nombre de résultats à ignorer|
|adultContent|string|no|query|(aucun) |Filtrage de contenu pour adultes. Valeurs valides : <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(aucun) |Marché ou région pour affiner la recherche (exemple : fr-FR)|
|longitude|number|no|query|(aucun) |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450)|
|latitude|number|no|query|(aucun) |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696)|
|webFileType|string|no|query|(aucun) |Type de fichier pour affiner la recherche (exemple : « DOC »)|
|videoFilters|string|no|query|(aucun) |Recherche en fonction de la taille, de l’aspect, de la couleur, du style, du visage ou de toute combinaison de ces critères. Valeurs valides : <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Par exemple : « Duration:Short+Resolution:High »|
|videoSortBy|string|no|query|(aucun) |Ordre de tri des résultats. Valeurs valides : <ul><li>Date</li><li>Relevance</li></ul> <p>L’ordre de tri en fonction de la date est décroissant.</p>|
|imageFilters|string|no|query|(aucun) |Recherche en fonction de la taille, de l’aspect, de la couleur, du style, du visage ou de toute combinaison de ces critères. Valeurs valides : <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Par exemple : « Size:Small+Aspect:Square »|
|newsSortBy|string|no|query|(aucun) |Ordre de tri des résultats. Valeurs valides : <ul><li>Date</li><li>Relevance</li></ul> <p>L’ordre de tri en fonction de la date est décroissant.</p>|
|newsCategory|string|no|query|(aucun) |Catégorie d’actualités pour affiner la recherche (exemple : « rt\_Business »)|
|newsLocationOverride|string|no|query|(aucun) |Remplacement pour la détection du lieu par Bing. Ce paramètre s’applique uniquement au marché fr-FR. Le format d’entrée est « US ».<state /> (Exemple : « US.WA »)|

#### Response
|Nom|Description|
|---|---|
|200|OK|
|default|L’opération a échoué.|


## Définitions d’objet

#### WebResultModel : résultats de la recherche sur le web par Bing

|Nom de la propriété | Type de données | Requis |
|---|---|---|
|Intitulé|string|no|
|Description|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|FullUrl|string|no|

#### VideoResultModel : résultats de la recherche de vidéos par Bing

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|Intitulé|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|MediaUrl|string|no|
|Runtime|integer|no|
|Miniature|non défini|no|

#### ThumbnailModel : propriétés de miniature de l’élément multimédia

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|MediaUrl|string|no|
|ContentType|string|no|
|Largeur|integer|no|
|Hauteur|integer|no|
|FileSize|integer|no|

#### ImageResultModel : résultats de la recherche d’images par Bing

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|Intitulé|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|MediaUrl|string|no|
|SourceUrl|string|no|
|Miniature|non défini|no|

#### NewsResultModel : résultats de la recherche d’actualités par Bing

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|Intitulé|string|no|
|Description|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|Source|string|no|
|Date|string|no|

#### SpellResultModel : résultats des suggestions orthographiques de Bing

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|ID|string|no|
|Valeur|string|no|

#### RelatedSearchResultModel : résultats de la recherche liée par Bing

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|Intitulé|string|no|
|ID|string|no|
|BingUrl|string|no|

#### CompositeSearchResultModel : résultats de la recherche composite par Bing

|Nom de la propriété | Type de données |Requis |
|---|---|---|
|WebResultsTotal|integer|no|
|ImageResultsTotal|integer|no|
|VideoResultsTotal|integer|no|
|NewsResultsTotal|integer|no|
|SpellSuggestionsTotal|integer|no|
|WebResults|array|no|
|ImageResults|array|no|
|VideoResults|array|no|
|NewsResults|array|no|
|SpellSuggestionResults|array|no|
|RelatedSearchResults|array|no|

## Étapes suivantes

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).

<!---HONumber=AcomDC_0525_2016-->