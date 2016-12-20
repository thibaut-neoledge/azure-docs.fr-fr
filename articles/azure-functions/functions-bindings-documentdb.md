---
title: Liaisons DocumentDB Azure Functions | Microsoft Docs
description: "Découvrez comment utiliser des liaisons Azure DocumentDB dans Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/10/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 3c406de579e3f09b521b60861230106c952f4357


---
# <a name="azure-functions-documentdb-bindings"></a>Liaisons DocumentDB Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons Azure DocumentDB dans Azure Functions. Azure Functions prend en charge des liaisons d’entrée et de sortie pour DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Pour plus d’informations sur DocumentDB, consultez [Présentation de DocumentDB](../documentdb/documentdb-introduction.md) et [Générer une application de console DocumentDB](../documentdb/documentdb-get-started.md).

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>Liaison d’entrée DocumentDB
La liaison d’entrée DocumentDB récupère un document DocumentDB et le transmet au paramètre d’entrée nommé de la fonction. L’ID du document peut être déterminé en fonction du déclencheur qui appelle la fonction. 

L’entrée DocumentDB d’une fonction utilise l’objet JSON suivant dans le tableau `bindings` de function.json :

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "id": "<Id of the DocumentDB document - see below>",
  "connection": "<Name of app setting with connection string - see below>",
  "direction": "in"
},
```

Notez les points suivants :

* `id` prend en charge des liaisons semblables à `{queueTrigger}`, qui utilise la valeur de chaîne du message de file d’attente en tant qu’ID de document.
* `connection` doit être le nom d’un paramètre d’application qui pointe vers le point de terminaison pour votre compte DocumentDB (avec la valeur `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). Si vous créez un compte DocumentDB par le biais de l’interface utilisateur du portail de Functions, le processus de création du compte crée automatiquement un paramètre d’application. Pour utiliser un compte DocumentDB existant, vous devez [configurer ce paramètre d’application manuellement](). 
* Si le document spécifié est introuvable, le paramètre d’entrée nommé de la fonction est défini sur `null`. 

## <a name="input-usage"></a>Utilisation en entrée
Cette section vous montre comment utiliser la liaison d’entrée DocumentDB dans le code de votre fonction.

Dans les fonctions C# et F#, toutes les modifications apportées au document d’entrée (paramètre d’entrée nommé) sont automatiquement renvoyées à la collection une fois que la fonction s’est correctement terminée. Dans les fonctions Node.js, les mises à jour du document dans la liaison d’entrée ne sont pas renvoyées à la collection. Toutefois, vous pouvez utiliser `context.bindings.<documentName>In` et `context.bindings.<documentName>Out` pour apporter des mises à jour aux documents d’entrée. Pour en avoir un aperçu, consultez [l’exemple Node.js](#innodejs).

<a name="inputsample"></a>

## <a name="input-sample"></a>Exemple d’entrée
Supposez que le tableau `bindings` de function.json contient la liaison d’entrée DocumentDB suivante :

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "in"
}
```

Consultez l’exemple dans le langage de votre choix pour voir comment utiliser cette liaison d’entrée pour mettre à jour la valeur de texte du document.

* [C#](#incsharp)
* [F#](#infsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Exemple d’entrée en C# #

```cs
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Exemple d’entrée en F# #

```fsharp
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

Vous devez ajouter un fichier `project.json` qui spécifie les dépendances NuGet `FSharp.Interop.Dynamic` et `Dynamitey` :

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Pour ajouter un fichier `project.json`, consultez [F# package management](functions-reference-fsharp.md#package) (Gestion des packages F#).

<a name="innodejs"></a>

### <a name="input-sample-in-nodejs"></a>Exemple d’entrée en Node.js

```javascript
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="a-iddocdboutputadocumentdb-output-binding"></a><a id="docdboutput"></a>Liaison de sortie DocumentDB
La liaison de sortie DocumentDB vous permet d’écrire un nouveau document dans une base de données Azure DocumentDB. 

La liaison de sortie utilise l’objet JSON suivant dans le tableau `bindings` de function.json : 

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "createIfNotExists": <true or false - see below>,
  "connection": "<Value of AccountEndpoint in Application Setting - see below>",
  "direction": "out"
}
```

Notez les points suivants :

* Définissez `createIfNotExists` sur `true` pour créer la base de données et la collection si elle n’existe pas. La valeur par défaut est `false`. Les collections sont créées avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d’informations, consultez [Tarification de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).
* `connection` doit être le nom d’un paramètre d’application qui pointe vers le point de terminaison pour votre compte DocumentDB (avec la valeur `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`). Si vous créez un compte DocumentDB par le biais de l’interface utilisateur du portail de Functions, le processus de création du compte crée automatiquement un paramètre d’application. Pour utiliser un compte DocumentDB existant, vous devez [configurer ce paramètre d’application manuellement](). 

## <a name="output-usage"></a>Utilisation en sortie
Cette section vous montre comment utiliser la liaison de sortie DocumentDB dans le code de votre fonction.

Si vous écrivez dans le paramètre de sortie de votre fonction, par défaut, un nouveau document est généré dans votre base de données avec un GUID généré automatiquement en tant qu’ID de document. Vous pouvez spécifier l’ID du document de sortie en spécifiant la propriété JSON `id` dans le paramètre de sortie. Si un document avec cet ID existe déjà, le document de sortie le remplace. 

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemple de sortie
Supposez que le tableau `bindings` de function.json contient la liaison de sortie DocumentDB suivante :

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "out"
}
```

Et que vous disposez d’une liaison d’entrée de file d’attente pour une file d’attente qui reçoit le code JSON dans le format suivant :

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Et que vous souhaitez créer des documents DocumentDB dans le format suivant pour chaque enregistrement :

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Consultez l’exemple dans le langage de votre choix pour voir comment utiliser cette liaison de sortie pour ajouter des documents à votre base de données.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemple de sortie en C# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
{
  log.Info($"C# Queue trigger function processed: {myQueueItem}");

  dynamic employee = JObject.Parse(myQueueItem);

  employeeDocument = new {
    id = employee.name + "-" + employee.employeeId,
    name = employee.name,
    employeeId = employee.employeeId,
    address = employee.address
  };
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Exemple de sortie en F# #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

Vous devez ajouter un fichier `project.json` qui spécifie les dépendances NuGet `FSharp.Interop.Dynamic` et `Dynamitey` :

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

Pour ajouter un fichier `project.json`, consultez [F# package management](functions-reference-fsharp.md#package) (Gestion des packages F#).

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Exemple de sortie en Node.js

```javascript
module.exports = function (context) {

  context.bindings.employeeDocument = JSON.stringify({ 
    id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
    name: context.bindings.myQueueItem.name,
    employeeId: context.bindings.myQueueItem.employeeId,
    address: context.bindings.myQueueItem.address
  });

  context.done();
};
```


<!--HONumber=Nov16_HO3-->


