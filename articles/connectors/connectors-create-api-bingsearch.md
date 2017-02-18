---
title: "Ajouter le connecteur Recherche Bing à des applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur Bing Search avec les paramètres de l’API REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: a7f530e8-1573-4612-8899-c9c84aa2de6d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: dbb920f212d46365233e83ba1e0a0ae99917e405


---
# <a name="get-started-with-the-bing-search-connector"></a>Prise en main du connecteur Bing Search
Connecte-vous à Bing Search pour rechercher des actualités, des vidéos et bien plus encore. Avec Bing Search, vous pouvez effectuer les opérations suivantes : 

* Créer votre flux d’activité en fonction des données que vous obtenez pendant votre recherche. 
* Utiliser des actions pour rechercher des images, des actualités et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, vous pouvez rechercher une vidéo, puis utiliser Twitter pour la publier dans un flux Twitter.

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Bing Search inclut les actions suivantes. Il n’y a aucun déclencheur. 

| Déclencheurs | Actions |
| --- | --- |
| Aucun |<ul><li>Rechercher sur le web</li><li>Rechercher ded vidéos</li><li>Rechercher des images</li><li>Rechercher des actualités</li><li>Recherche liée</li><li>Rechercher l’orthographe de mots</li><li>Rechercher tout</li></ul> |

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## <a name="swagger-rest-api-reference"></a>Informations de référence sur l'API REST Swagger
S’applique à la version 1.0.

### <a name="search-web"></a>Rechercher sur le web
Récupère des sites web à partir d’une recherche Bing.  
```GET: /Web```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte à rechercher (exemple : « xbox ») |
| maxResult |integer |no |query |Aucun |Nombre maximal de résultats à renvoyer |
| startOffset |integer |no |query |Aucun |Nombre de résultats à ignorer |
| adultContent |string |no |query |Aucun |Filtrage de contenu pour adultes. Valeurs valides :  <ul><li>Off</li><li>Modéré</li><li>Strict</li></ul> |
| market |string |no |query |Aucun |Marché ou région pour affiner la recherche (exemple : en-US) |
| longitude |number |no |query |Aucun |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450) |
| latitude |number |no |query |Aucun |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696) |
| webFileType |string |no |query |Aucun |Type de fichier pour affiner la recherche (exemple : « DOC ») |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="search-videos"></a>Rechercher ded vidéos
Récupère des vidéos à partir d’une recherche Bing.  
```GET: /Video```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte à rechercher (exemple : « xbox ») |
| maxResult |integer |no |query |Aucun |Nombre maximal de résultats à renvoyer |
| startOffset |integer |no |query |Aucun |Nombre de résultats à ignorer |
| adultContent |string |no |query |Aucun |Filtrage de contenu pour adultes. Valeurs valides :  <ul><li>Off</li><li>Modéré</li><li>Strict</li></ul> |
| market |string |no |query |Aucun |Marché ou région pour affiner la recherche (exemple : en-US) |
| longitude |number |no |query |Aucun |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450) |
| latitude |number |no |query |Aucun |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696) |
| videoFilters |string |no |query |Aucun |Recherche en fonction de la taille, de l’aspect, de la couleur, du style, du visage ou de toute combinaison de ces critères.  Valeurs valides :  <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Par exemple : « Duration:Short+Resolution:High » |
| videoSortBy |string |no |query |Aucun |Ordre de tri des résultats. Valeurs valides :  <ul><li>Date</li><li>Pertinence</li></ul> <p>L’ordre de tri par date implique un ordre décroissant.</p> |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="search-images"></a>Rechercher des images
Récupère des images à partir d’une recherche Bing.  
```GET: /Image```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte à rechercher (exemple : « xbox ») |
| maxResult |integer |no |query |Aucun |Nombre maximal de résultats à renvoyer |
| startOffset |integer |no |query |Aucun |Nombre de résultats à ignorer |
| adultContent |string |no |query |Aucun |Filtrage de contenu pour adultes. Valeurs valides :  <ul><li>Off</li><li>Modéré</li><li>Strict</li></ul> |
| market |string |no |query |Aucun |Marché ou région pour affiner la recherche (exemple : en-US) |
| longitude |number |no |query |Aucun |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450) |
| latitude |number |no |query |Aucun |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696) |
| imageFilters |string |no |query |Aucun |Recherche en fonction de la taille, de l’aspect, de la couleur, du style, du visage ou de toute combinaison de ces critères. Valeurs valides :  <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[largeur]</li><li>Size:Height:[hauteur]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Par exemple : « Size:Small+Aspect:Square » |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="search-news"></a>Rechercher des actualités
Récupère des résultats d’actualités à partir d’une recherche Bing.  
```GET: /News```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte à rechercher (exemple : « xbox ») |
| maxResult |integer |no |query |Aucun |Nombre maximal de résultats à renvoyer |
| startOffset |integer |no |query |Aucun |Nombre de résultats à ignorer |
| adultContent |string |no |query |Aucun |Filtrage de contenu pour adultes. Valeurs valides :  <ul><li>Off</li><li>Modéré</li><li>Strict</li></ul> |
| market |string |no |query |Aucun |Marché ou région pour affiner la recherche (exemple : en-US) |
| longitude |number |no |query |Aucun |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450) |
| latitude |number |no |query |Aucun |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696) |
| newsSortBy |string |no |query |Aucun |Ordre de tri des résultats. Valeurs valides :  <ul><li>Date</li><li>Pertinence</li></ul> <p>L’ordre de tri par date implique un ordre décroissant.</p> |
| newsCategory |string |no |query | |Catégorie d’actualités pour affiner la recherche (exemple : « rt_Business ») |
| newsLocationOverride |string |no |query |Aucun |Remplacement pour la détection du lieu par Bing. Ce paramètre s’applique uniquement au marché en-US. Le format d’entrée est « US ».<state /> (Exemple : « US.WA ») |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="search-spellings"></a>Rechercher l’orthographe de mots
Récupère des suggestions orthographiques.  
```GET: /SpellingSuggestions```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte à rechercher (exemple : « xbox ») |
| maxResult |integer |no |query |Aucun |Nombre maximal de résultats à renvoyer |
| startOffset |integer |no |query |Aucun |Nombre de résultats à ignorer |
| adultContent |string |no |query |Aucun |Filtrage de contenu pour adultes. Valeurs valides :  <ul><li>Off</li><li>Modéré</li><li>Strict</li></ul> |
| market |string |no |query |Aucun |Marché ou région pour affiner la recherche (exemple : en-US) |
| longitude |number |no |query |Aucun |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450) |
| latitude |number |no |query |Aucun |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696) |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="search-related"></a>Recherche liée
Récupère des résultats de recherche liée à partir d’une recherche Bing.  
```GET: /RelatedSearch```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte à rechercher (exemple : « xbox ») |
| maxResult |integer |no |query |Aucun |Nombre maximal de résultats à renvoyer |
| startOffset |integer |no |query |Aucun |Nombre de résultats à ignorer |
| adultContent |string |no |query |Aucun |Filtrage de contenu pour adultes. Valeurs valides :  <ul><li>Off</li><li>Modéré</li><li>Strict</li></ul> |
| market |string |no |query |Aucun |Marché ou région pour affiner la recherche (exemple : en-US) |
| longitude |number |no |query |Aucun |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450) |
| latitude |number |no |query |Aucun |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696) |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

### <a name="search-all"></a>Rechercher tout
Récupère la totalité des sites web, vidéos, images, etc. à partir d’une recherche Bing.  
```GET: /CompositeSearch```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| query |string |yes |query |Aucun |Texte à rechercher (exemple : « xbox ») |
| maxResult |integer |no |query |Aucun |Nombre maximal de résultats à renvoyer |
| startOffset |integer |no |query |Aucun |Nombre de résultats à ignorer |
| adultContent |string |no |query |Aucun |Filtrage de contenu pour adultes. Valeurs valides :  <ul><li>Off</li><li>Modéré</li><li>Strict</li></ul> |
| market |string |no |query |Aucun |Marché ou région pour affiner la recherche (exemple : en-US) |
| longitude |number |no |query |Aucun |Longitude (coordonnée est/ouest) pour affiner la recherche (exemple : 47.603450) |
| latitude |number |no |query |Aucun |Latitude (coordonnée nord/sud) pour affiner la recherche (exemple : -122.329696) |
| webFileType |string |no |query |Aucun |Type de fichier pour affiner la recherche (exemple : « DOC ») |
| videoFilters |string |no |query |Aucun |Recherche en fonction de la taille, de l’aspect, de la couleur, du style, du visage ou de toute combinaison de ces critères.  Valeurs valides :  <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>Par exemple : « Duration:Short+Resolution:High » |
| videoSortBy |string |no |query |Aucun |Ordre de tri des résultats. Valeurs valides :  <ul><li>Date</li><li>Pertinence</li></ul> <p>L’ordre de tri par date implique un ordre décroissant.</p> |
| imageFilters |string |no |query |Aucun |Recherche en fonction de la taille, de l’aspect, de la couleur, du style, du visage ou de toute combinaison de ces critères. Valeurs valides :  <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[largeur]</li><li>Size:Height:[hauteur]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>Par exemple : « Size:Small+Aspect:Square » |
| newsSortBy |string |no |query |Aucun |Ordre de tri des résultats. Valeurs valides :  <ul><li>Date</li><li>Pertinence</li></ul> <p>L’ordre de tri par date implique un ordre décroissant.</p> |
| newsCategory |string |no |query |(aucun) |Catégorie d’actualités pour affiner la recherche (exemple : « rt_Business ») |
| newsLocationOverride |string |no |query |Aucun |Remplacement pour la détection du lieu par Bing. Ce paramètre s’applique uniquement au marché en-US. Le format d’entrée est « US ».<state /> (Exemple : « US.WA ») |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d’objet
#### <a name="webresultmodel-bing-web-search-results"></a>WebResultModel : résultats de la recherche sur le web par Bing
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Intitulé |string |no |
| Description |string |no |
| DisplayUrl |string |no |
| ID |string |no |
| FullUrl |string |no |

#### <a name="videoresultmodel-bing-video-search-results"></a>VideoResultModel : résultats de la recherche de vidéos par Bing
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Intitulé |string |no |
| DisplayUrl |string |no |
| ID |string |no |
| MediaUrl |string |no |
| Runtime |integer |no |
| Miniature |non défini |no |

#### <a name="thumbnailmodel-thumbnail-properties-of-the-multimedia-element"></a>ThumbnailModel : propriétés de miniature de l’élément multimédia
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| MediaUrl |string |no |
| ContentType |string |no |
| Largeur |integer |no |
| Hauteur |integer |no |
| FileSize |integer |no |

#### <a name="imageresultmodel-bing-image-search-results"></a>ImageResultModel : résultats de la recherche d’images par Bing
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Intitulé |string |no |
| DisplayUrl |string |no |
| ID |string |no |
| MediaUrl |string |no |
| SourceUrl |string |no |
| Miniature |non défini |no |

#### <a name="newsresultmodel-bing-news-search-results"></a>NewsResultModel : résultats de la recherche d’actualités par Bing
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Intitulé |string |no |
| Description |string |no |
| DisplayUrl |string |no |
| ID |string |no |
| Source |string |no |
| Date |string |no |

#### <a name="spellresultmodel-bing-spelling-suggestions-results"></a>SpellResultModel : résultats des suggestions orthographiques de Bing
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |no |
| Valeur |string |no |

#### <a name="relatedsearchresultmodel-bing-related-search-results"></a>RelatedSearchResultModel : résultats de la recherche liée par Bing
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Intitulé |string |no |
| ID |string |no |
| BingUrl |string |no |

#### <a name="compositesearchresultmodel-bing-composite-search-results"></a>CompositeSearchResultModel : résultats de la recherche composite par Bing
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| WebResultsTotal |integer |no |
| ImageResultsTotal |integer |no |
| VideoResultsTotal |integer |no |
| NewsResultsTotal |integer |no |
| SpellSuggestionsTotal |integer |no |
| WebResults |array |no |
| ImageResults |array |no |
| VideoResults |array |no |
| NewsResults |array |no |
| SpellSuggestionResults |array |no |
| RelatedSearchResults |array |no |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).




<!--HONumber=Jan17_HO3-->


