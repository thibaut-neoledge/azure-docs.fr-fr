---
title: "Gérer les types de contenu - Azure Logic Apps | Microsoft Docs"
description: "Découvrir comment les applications logiques gèrent les types de contenu au moment de la conception et de l’exécution"
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
ms.author: LADocs; jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: ac67838344bbd10384299c086ff096fbe5dec6a9
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# <a name="handle-content-types-in-logic-apps"></a>Gérer les types de contenu dans les applications logiques

De nombreux types de contenu peuvent transiter par une application logique, notamment les données binaires, les fichiers plats, ainsi que les contenus XML et JSON. Si le moteur Logic Apps prend en charge tous les types de contenu, certains sont compris en mode natif. D’autres peuvent nécessiter un transtypage ou des conversions en fonction des besoins. Cet article décrit comment le moteur gère les différents types de contenu et comment gérer correctement ces types lorsque cela est nécessaire.

## <a name="content-type-header"></a>Entête Content-Type

Pour démarrer simplement, examinons les deux `Content-Types` qui ne nécessitent pas l’utilisation d’une conversion ou d’un transtypage dans une application logique : `application/json` et `text/plain`.

## <a name="applicationjson"></a>Application/JSON

Le moteur de flux de travail s’appuie sur l’en-tête `Content-Type` des appels HTTP pour déterminer la gestion appropriée. Toute demande comportant le type de contenu `application/json` est stockée et gérée comme un objet JSON. Le contenu JSON peut également être analysé par défaut sans subir de transtypage. 

Par exemple, vous pouvez analyser une demande comportant un en-tête de type de contenu `application/json ` dans un flux de travail à l’aide d’une expression telle que `@body('myAction')['foo'][0]` pour obtenir la valeur `bar` dans ce cas :

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

Aucun transtypage supplémentaire n’est nécessaire. Si vous utilisez des données JSON pour lesquelles aucun en-tête n’a été spécifié, vous pouvez les convertir manuellement en données JSON en utilisant la fonction `@json()` (par exemple : `@json(triggerBody())['foo']`).

### <a name="schema-and-schema-generator"></a>Schéma et générateur de schéma

Le déclencheur de requête vous permet d’entrer un schéma JSON pour la charge utile que vous vous attendez à recevoir. Ce schéma permet au concepteur de générer des jetons pour vous aider à utiliser le contenu de la demande. Si aucun schéma n’est disponible, sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma** afin de générer un schéma JSON à partir d’un exemple de charge utile.

![Schéma](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>Action d’analyse JSON

L’action `Parse JSON` vous permet d’analyser le contenu JSON en jetons conviviaux à utiliser dans l’application logique. À l’instar du déclencheur de requête, cette action vous permet d’entrer ou de générer un schéma JSON pour le contenu que vous souhaitez analyser. Cet outil facilite l’utilisation des données de Service Bus, Azure Cosmos DB, etc.

![Analyse JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>Text/plain

À l’image de `application/json`, les messages HTTP reçus avec l’en-tête `Content-Type` `text/plain` sont stockés dans leur forme brute. En outre, si ces messages sont inclus dans les actions suivantes sans transtypage, ces demandes sortent avec l’en-tête`Content-Type` : `text/plain`. Par exemple, lorsque vous travaillez avec un fichier plat, vous pouvez obtenir ce contenu HTTP en tant que `text/plain` :

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

Si dans l’action suivante vous envoyez la demande en tant que corps d’une autre demande (`@body('flatfile')`), celle-ci a un en-tête Content-Type `text/plain`. Si vous utilisez des données de texte brut pour lesquelles aucun en-tête n’a été spécifié, vous pouvez les convertir manuellement en données texte en utilisant la fonction `@string()` (par exemple : `@string(triggerBody())`).

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml, Application/octet-stream et fonctions de conversion

Le moteur Logic Apps conserve toujours le `Content-Type` reçu sur la demande ou réponse HTTP. Par conséquent, si le moteur reçoit le contenu avec le `Content-Type` de `application/octet-stream`, et que vous incluez le contenu dans une action ultérieure sans transtypage, la demande sortante a pour en-tête `Content-Type` : `application/octet-stream`. De cette façon, le moteur garantit qu’aucune donnée n’est perdue lors du déplacement dans le flux de travail. Toutefois, les états d’action (entrées et sorties) sont parallèlement stockés dans un objet JSON au fur et à mesure que l’état se déplace ans le flux de travail. Pour préserver certains types de données, le moteur convertit le contenu en une chaîne binaire encodée en base64, avec les métadonnées appropriées, qui conserve `$content` et `$content-type`, automatiquement convertis. 

* `@json()` : convertit les données en `application/json`
* `@xml()` : convertit les données en `application/xml`
* `@binary()` : convertit les données en `application/octet-stream`
* `@string()` : convertit les données en `text/plain`
* `@base64()` : convertit le contenu en une chaîne encodée en base64
* `@base64toString()` : convertit une chaîne encodée en base64 en `text/plain`
* `@base64toBinary()` : convertit une chaîne encodée en base64 en `application/octet-stream`
* `@encodeDataUri()` : encode une chaîne en tableau d’octets dataUri
* `@decodeDataUri()` : décode un dataUri en tableau d’octets

Par exemple, si vous recevez une demande HTTP avec `Content-Type` : `application/xml` :

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Vous pouvez appliquer un transtypage et utiliser le résultat ultérieurement avec, par exemple, `@xml(triggerBody())` ou dans une fonction comme `@xpath(xml(triggerBody()), '/CustomerName')`.

## <a name="other-content-types"></a>Autres types de contenu

D’autres types de contenu sont pris en charge et fonctionnent avec des applications logiques, mais vous pouvez être amené à récupérer manuellement le corps du message en décodant le `$content`. Supposons par exemple que vous déclenchiez une requête `application/x-www-url-formencoded` où `$content` est la charge utile encodée sous la forme d’une chaîne en base64 pour préserver toutes les données :

```
CustomerName=Frank&Address=123+Avenue
```

Étant donné que la demande n’est pas en texte brut ou JSON, elle est stockée dans l’action comme suit :

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Dans la mesure où il n’existe actuellement pas de fonction native pour les données de formulaire, vous pouvez toujours utiliser ces données dans un flux de travail en y accédant manuellement avec une fonction comme `@string(body('formdataAction'))`. Pour que la demande sortante ait également l’en-tête de type de contenu `application/x-www-url-formencoded`, vous pouvez l’ajouter au corps de l’action sans transtypage comme suit : `@body('formdataAction')`. Toutefois, cette méthode ne fonctionne que si le corps est le seul paramètre dans l’entrée `body`. Si vous essayez d’utiliser `@body('formdataAction')` dans une demande `application/json`, vous obtenez une erreur d’exécution, car le corps codé est envoyé.


