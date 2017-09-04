---
title: Liaisons Mobile Apps Azure Functions| Microsoft Docs
description: "Découvrez comment utiliser des liaisons Azure Mobile Apps dans Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: d2c0e4e233761584bad2df05a8e702e4fc77e84f
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-mobile-apps-bindings"></a>Liaisons Azure Mobile Apps Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) dans Azure Functions. Azure Functions prend en charge des liaisons d’entrée et de sortie pour Mobile Apps.

Les liaisons d’entrée et de sortie Mobile Apps vous permettent d’effectuer des opérations de [lecture et écriture dans des tables de données](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations), dans votre application mobile.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Liaison d’entrée Mobile Apps
La liaison d’entrée Mobile Apps charge un enregistrement à partir d’un point de terminaison de table mobile et le transmet à votre fonction. Dans des fonctions C# et F#, toutes les modifications apportées à l’enregistrement sont automatiquement renvoyées à la table une fois que la fonction s’est correctement terminée.

L’entrée Mobile Apps d’une fonction utilise l’objet JSON suivant dans le tableau `bindings` de function.json :

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

Notez les points suivants :

* L’élément `id` peut être statique ou basé sur le déclencheur qui appelle la fonction. Par exemple, si vous utilisez un [déclencheur de file d’attente]() pour votre fonction, `"id": "{queueTrigger}"` utilise la valeur de chaîne du message de file d’attente en tant qu’ID de l’enregistrement à récupérer.
* `connection` doit contenir le nom d’un paramètre d’application de votre application de fonction, comportant l’URL de votre application mobile. La fonction utilise cette URL pour construire les opérations REST requises par rapport à votre application mobile. Vous [créez un paramètre d’application dans votre application de fonction](), contenant l’URL de votre application mobile (de type `http://<appname>.azurewebsites.net`), puis spécifiez le nom du paramètre d’application dans la propriété `connection` de votre liaison d’entrée. 
* Vous devez spécifier `apiKey` si vous [implémentez une clé API dans le service principal de votre application mobile Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) ou [implémentez une clé API dans le service principal de votre application mobile .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Pour ce faire, vous [créez un paramètre d’application dans votre application de fonction](), contenant la clé API, puis ajoutez la propriété `apiKey` dans votre liaison d’entrée avec le nom du paramètre d’application. 
  
  > [!IMPORTANT]
  > Cette clé API ne doit pas être partagée avec vos clients d’application mobile. Elle doit uniquement être distribuée de façon sécurisée aux clients côté service, comme Azure Functions. 
  > 
  > [!NOTE]
  > Azure Functions stocke vos informations de connexion et les clés API en tant que paramètres d’application, de sorte qu’elles ne soient pas vérifiées dans votre référentiel de contrôle de code source. Ceci protège vos informations sensibles.
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>Utilisation en entrée
Cette section vous montre comment utiliser la liaison d’entrée Mobile Apps dans le code de votre fonction. 

Lorsque l’enregistrement correspondant à la table et à l’ID d’enregistrement spécifiés est trouvé, il est transmis au paramètre [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) nommé (ou, dans Node.js, à l’objet `context.bindings.<name>`). Si l’enregistrement est introuvable, le paramètre présente la valeur `null`. 

Dans les fonctions C# et F#, toutes les modifications apportées à l’enregistrement d’entrée (paramètre d’entrée) sont automatiquement renvoyées à la table Mobile Apps une fois que la fonction s’est correctement terminée. Dans les fonctions Node.js, utilisez `context.bindings.<name>` pour accéder à l’enregistrement d’entrée. Vous ne pouvez pas modifier un enregistrement dans Node.js.

<a name="inputsample"></a>

## <a name="input-sample"></a>Exemple d’entrée
Supposons le code function.json suivant, qui récupère un enregistrement de table Mobile Apps avec l’ID du message de déclenchement de file d’attente :

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```

Consultez l’exemple dans le langage de votre choix pour voir comment utiliser l’enregistrement d’entrée à partir de la liaison. Les exemples en C# et F# modifient également la propriété `text` de l’enregistrement.

* [C#](#inputcsharp)
* [Node.JS](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Exemple d’entrée en C# #

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Exemple d’entrée en Node.js

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Liaison de sortie Mobile Apps
Utilisez la liaison de sortie Mobile Apps pour écrire un nouvel enregistrement dans un point de terminaison de table Mobile Apps.  

La sortie Mobile Apps d’une fonction utilise l’objet JSON suivant dans le tableau `bindings` de function.json :

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

Notez les points suivants :

* `connection` doit contenir le nom d’un paramètre d’application de votre application de fonction, comportant l’URL de votre application mobile. La fonction utilise cette URL pour construire les opérations REST requises par rapport à votre application mobile. Vous [créez un paramètre d’application dans votre application de fonction](), contenant l’URL de votre application mobile (de type `http://<appname>.azurewebsites.net`), puis spécifiez le nom du paramètre d’application dans la propriété `connection` de votre liaison d’entrée. 
* Vous devez spécifier `apiKey` si vous [implémentez une clé API dans le service principal de votre application mobile Node.js](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) ou [implémentez une clé API dans le service principal de votre application mobile .NET](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Pour ce faire, vous [créez un paramètre d’application dans votre application de fonction](), contenant la clé API, puis ajoutez la propriété `apiKey` dans votre liaison d’entrée avec le nom du paramètre d’application. 
  
  > [!IMPORTANT]
  > Cette clé API ne doit pas être partagée avec vos clients d’application mobile. Elle doit uniquement être distribuée de façon sécurisée aux clients côté service, comme Azure Functions. 
  > 
  > [!NOTE]
  > Azure Functions stocke vos informations de connexion et les clés API en tant que paramètres d’application, de sorte qu’elles ne soient pas vérifiées dans votre référentiel de contrôle de code source. Ceci protège vos informations sensibles.
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilisation en sortie
Cette section vous montre comment utiliser la liaison de sortie Mobile Apps dans le code de votre fonction. 

Dans les fonctions C#, utilisez un paramètre de sortie nommé de type `out object` pour accéder à l’enregistrement de sortie. Dans les fonctions Node.js, utilisez `context.bindings.<name>` pour accéder à l’enregistrement de sortie.

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemple de sortie
Supposons le code function.json suivant, qui définit un déclencheur de file d’attente et une sortie Mobile Apps :

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

Consultez l’exemple dans le langage de votre choix pour voir comment créer un enregistrement dans le point de terminaison de la table Mobile Apps avec le contenu du message de file d’attente.

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemple de sortie en C# #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Exemple de sortie en Node.js

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


