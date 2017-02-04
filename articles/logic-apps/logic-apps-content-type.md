---
title: Gestion du type de contenu des applications logiques | Microsoft Docs
description: "Découvrez comment les applications logiques gèrent les types de contenu au moment de la conception et de l’exécution"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: f65e5c42a42dfaef8100935d979375bcb7a06e92


---
# <a name="logic-apps-content-type-handling"></a>Gestion du type de contenu des applications logiques
Il existe de nombreux types de contenu qui peuvent transiter par une application logique, notamment les données binaires, les fichiers plats, ainsi que les contenus XML et JSON.  Bien que tous les types de contenu soient pris en charge, certains sont compris en mode natif par le moteur d’applications logique, tandis que d’autres peuvent nécessiter un transtypage ou des conversions en fonction des besoins.  L’article suivant explique comment le moteur gère différents types de contenu et comment ils peuvent être correctement gérés en fonction des besoins.

## <a name="content-type-header"></a>Entête Content-Type
Pour démarrer simplement, examinons les deux `Content-Types` qui ne nécessitent pas l’utilisation d’une conversion ou d’un transtypage dans une application logique : `application/json` et `text/plain`.

### <a name="applicationjson"></a>Application/json
Le moteur de flux de travail s’appuie sur l’en-tête `Content-Type` des appels HTTP pour déterminer la gestion appropriée.  Toute demande comportant le type de contenu `application/json` est stockée et gérée comme un objet JSON.  En outre, le contenu JSON peut être analysé par défaut sans subir de transtypage.  Ainsi, une demande comportant l’en-tête de type de contenu `application/json ` comme celle-ci :

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

peut être analysée dans un flux de travail avec une expression telle que `@body('myAction')['foo'][0]` pour obtenir une valeur (dans ce cas, `bar`).  Aucun transtypage supplémentaire n’est nécessaire.  Si vous utilisez des données JSON pour lesquelles aucun en-tête n’a été spécifié, vous pouvez les convertir manuellement en données JSON en utilisant la fonction `@json()` (par exemple : `@json(triggerBody())['foo']`).

### <a name="textplain"></a>Text/plain
À l’image de `application/json`, les messages HTTP reçus avec l’en-tête `Content-Type` `text/plain` sont stockés dans leur forme brute.  En outre, s’ils sont par la suite intégrés à des actions sans subir de transtypage, la demande est émise avec un en-tête `Content-Type` : `text/plain`.  Par exemple, si vous utilisez un fichier plat, vous pouvez recevoir le contenu HTTP suivant :

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

en tant que `text/plain`.  Si dans l’action suivante vous l’envoyez en tant que corps d’une autre demande (`@body('flatfile')`), celle-ci a un en-tête Content-Type `text/plain`.  Si vous utilisez des données de texte brut pour lesquelles aucun en-tête n’a été spécifié, vous pouvez les convertir manuellement en données texte en utilisant la fonction `@string()` (par exemple : `@string(triggerBody())`).

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml, Application/octet-stream et fonctions de conversion
Le moteur d’application logique conserve toujours le `Content-Type` qui a été reçu sur la requête ou réponse HTTP.  Cela signifie que si un contenu est reçu avec le `Content-Type` `application/octet-stream` sans transtypage, notamment le contenu dans une action suivante, la demande sortante a un `Content-Type` : `application/octet-stream`.  Ainsi, le moteur peut garantir que les données ne seront pas perdues au fil de leur progression dans le flux de travail.  Toutefois, les états d’action (entrées et sorties) sont parallèlement stockés dans un objet JSON.  Cela signifie que, pour préserver certains types de données, le moteur convertit le contenu en une chaîne binaire encodée en base64, avec les métadonnées appropriées, qui conserve `$content` et `$content-type`, automatiquement convertis.  Vous pouvez également effectuer des conversions manuelles entre des types de contenu à l’aide des fonctions de conversion intégrées :

* `@json()` : convertit les données en `application/json`
* `@xml()` : convertit les données en `application/xml`
* `@binary()` : convertit les données en `application/octet-stream`
* `@string()` : convertit les données en `text/plain`
* `@base64()` : convertit le contenu en une chaîne encodée en base64
* `@base64toString()` : convertit une chaîne encodée en base64 en `text/plain`
* `@base64toBinary()` : convertit une chaîne encodée en base64 en `application/octet-stream`
* `@encodeDataUri()` : encode une chaîne en tableau d’octets dataUri
* `@decodeDataUri()` : décode un dataUri en tableau d’octets

Par exemple, si vous recevez une requête HTTP avec `Content-Type` : `application/xml` telle que celle-ci :

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Vous pouvez appliquer un transtypage et utiliser le résultat ultérieurement avec, par exemple, `@xml(triggerBody())` ou dans une fonction comme `@xpath(xml(triggerBody()), '/CustomerName')`.

### <a name="other-content-types"></a>Autres types de contenu
D’autres types de contenu sont pris en charge et fonctionnent avec une application logique, mais vous pouvez être amené à récupérer manuellement le corps du message en décodant le `$content`.  Par exemple, supposons la demande `application/x-www-url-formencoded` suivante :

```
CustomerName=Frank&Address=123+Avenue
```

Dans la mesure où ce contenu n’est pas du texte brut ou de type JSON, il est stocké dans l’action sous la forme suivante :

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Où `$content` est la charge utile encodée sous la forme d’une chaîne en base64 pour préserver toutes les données.  Dans la mesure où il n’existe actuellement pas de fonction native pour les données de formulaire, nous pouvons toujours utiliser ces données dans un flux de travail en y accédant manuellement avec une fonction comme `@string(body('formdataAction'))`.  Pour que notre demande sortante ait également l’en-tête de type de contenu `application/x-www-url-formencoded`, nous pouvons simplement l’ajouter au corps de l’action sans transtypage comme suit : `@body('formdataAction')`.  Toutefois, cette opération ne fonctionne que si le corps est le seul paramètre dans l’entrée `body` .  Si vous essayez d’effectuer `@body('formdataAction')` à l’intérieur d’une demande `application/json`, vous obtenez une erreur d’exécution, car elle envoie le corps codé.




<!--HONumber=Jan17_HO3-->


