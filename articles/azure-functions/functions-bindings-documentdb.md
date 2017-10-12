---
title: Liaisons Azure Cosmos DB pour Azure Functions | Microsoft Docs
description: "Découvrez comment utiliser des déclencheurs et liaisons Azure Cosmos DB dans Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.openlocfilehash: ad058929eb888920823fddf549ada4ce2c6d9eee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-bindings-for-functions"></a>Liaisons Azure Cosmos DB pour Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons Azure Cosmos DB dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour Azure Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Pour plus d’informations sur l’informatique sans serveur avec Azure Cosmos DB, voir [Azure Cosmos DB : traitement de base de données sans serveur à l’aide d’Azure Functions](..\cosmos-db\serverless-computing-database.md).

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Déclencheur Azure Cosmos DB

Le déclencheur Azure Cosmos DB utilise le [flux de modification d’Azure Cosmos DB](../cosmos-db/change-feed.md) pour écouter les modifications sur plusieurs partitions. Le déclencheur nécessite une deuxième collection qu’il utilise pour stocker des _baux_ sur les partitions.

La collection surveillée et la collection contenant les baux doivent être disponibles pour que le déclencheur fonctionne.

Le déclencheur Azure Cosmos DB prend en charge les propriétés suivantes :

|Propriété  |Description  |
|---------|---------|
|**type** | Doit être défini sur `cosmosDBTrigger`. |
|**name** | Nom de variable utilisé dans le code de fonction, qui représente la liste des documents modifiés. | 
|**direction** | Doit être défini sur `in`. Ce paramètre est défini automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**connectionStringSetting** | Nom d’un paramètre d’application contenant la chaîne de connexion utilisée pour se connecter au compte Azure Cosmos DB surveillé. |
|**databaseName** | Nom de la base de données Azure Cosmos DB contenant la collection surveillée. |
|**collectionName** | Nom de la collection surveillée. |
| **leaseConnectionStringSetting** | (Facultatif) Nom d’un paramètre d’application contenant la chaîne de connexion au service stockant la collection de baux. S’il n’est pas défini, la valeur `connectionStringSetting` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. |
| **leaseDatabaseName** | (Facultatif) Nom de la base de données contenant la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur du paramètre `databaseName` est utilisée. Ce paramètre est automatiquement défini lorsque la liaison est créée dans le portail. |
| **leaseCollectionName** | (Facultatif) Nom de la collection utilisée pour stocker des baux. S’il n’est pas défini, la valeur `leases` est utilisée. |
| **createLeaseCollectionIfNotExists** | (Facultatif) Lorsque la valeur est définie sur `true`, la collection de baux est créée automatiquement si elle n’existe pas. La valeur par défaut est `false`. |
| **leaseCollectionThroughput** | (Facultatif) Définit la quantité d’unités de requête à attribuer lors de la création de la collection de baux. Ce paramètre est utilisé uniquement quand `createLeaseCollectionIfNotExists` est défini sur `true`. Ce paramètre est automatiquement défini lors de la création de la liaison à l’aide du portail.

>[!NOTE] 
>La chaîne de connexion utilisée pour se connecter à la collection de baux doit avoir des autorisations en écriture.

Ces propriétés peuvent être définies sous l’onglet Intégrer de la fonction dans le portail Azure ou en modifiant le fichier de projet `function.json`.

## <a name="using-an-azure-cosmos-db-trigger"></a>Utilisation d’un déclencheur Azure Cosmos DB

Cette section contient des exemples d’utilisation du déclencheur Azure Cosmos DB. Les exemples supposent des métadonnées de déclencheur qui ressemblent à ceci :

```json
{
  "type": "cosmosDBTrigger",
  "name": "documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "<connection-app-setting>",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```
 
Pour obtenir un exemple montrant comment créer un déclencheur Azure Cosmos DB à partir d’une application de fonction dans le portail, voir [Créer une fonction déclenchée par Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md). 

### <a name="trigger-sample-in-c"></a>Exemple de déclencheur en C# #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>Exemple de déclencheur en JavaScript
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>Liaison d’entrée d’API DocumentDB
La liaison d’entrée de l’API DocumentDB récupère un document Azure Cosmos DB et transmet celui-ci au paramètre d’entrée nommé de la fonction. L’ID du document peut être déterminé en fonction du déclencheur qui appelle la fonction. 

La liaison d’entrée d’API DocumentDB possède les propriétés suivantes dans *function.json* :

|Propriété  |Description  |
|---------|---------|
|**name**     | Nom du paramètre de liaison qui représente le document dans la fonction.  |
|**type**     | Doit être défini sur `documentdb`.        |
|**databaseName** | Base de données contenant le document.        |
|**collectionName**  | Nom de la collection qui contient le document. |
|**id**     | ID du document à récupérer. Cette propriété prend en charge les paramètres de liaison. Pour plus d’informations, consultez [Lier aux propriétés d’entrée personnalisées dans une expression de liaison](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression). |
|**sqlQuery**     | Requête SQL Azure Cosmos DB utilisée pour récupérer plusieurs documents. La requête prend en charge les liaisons d’exécution, telles que `SELECT * FROM c where c.departmentId = {departmentId}`.        |
|**Connexion**     |Nom du paramètre d’application contenant votre chaîne de connexion Azure Cosmos DB.        |
|**direction**     | Doit être défini sur `in`.         |

Vous ne pouvez pas définir à la fois la propriété **id** et la propriété **sqlQuery**. Si aucune propriété n’est définie, l’ensemble de la collection est récupéré.

## <a name="using-a-documentdb-api-input-binding"></a>Utiliser une liaison d’entrée d’API DocumentDB

* Dans les fonctions C# et F#, lorsque la fonction se termine correctement, toutes les modifications apportées au document d’entrée par le biais des paramètres d’entrée nommés sont automatiquement conservées. 
* Dans les fonctions JavaScript, les mises à jour ne sont pas effectuées une fois la fonction terminée. Utilisez plutôt `context.bindings.<documentName>In` et `context.bindings.<documentName>Out` pour effectuer les mises à jour. Consultez [l’exemple JavaScript](#injavascript).

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>Exemple d’entrée pour un seul document
Supposez que le tableau `bindings` de function.json contient la liaison d’entrée d’API DocumentDB suivante :

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_COSMOSDB",     
  "direction": "in"
}
```

Consultez l’exemple dans le langage de votre choix pour voir comment utiliser cette liaison d’entrée pour mettre à jour la valeur de texte du document.

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Exemple d’entrée en C# #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Exemple d’entrée en F# #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

Cet exemple requiert un fichier `project.json` qui spécifie les dépendances NuGet `FSharp.Interop.Dynamic` et `Dynamitey` :

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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>Exemple d’entrée en JavaScript

```javascript
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>Exemple d’entrée avec plusieurs documents

Supposons que vous souhaitiez récupérer plusieurs documents spécifiés par une requête SQL, à l’aide d’un déclencheur de file d’attente pour personnaliser les paramètres de requête. 

Dans cet exemple, le déclencheur de file d’attente fournit un paramètre `departmentId`. Un message de file d’attente de `{ "departmentId" : "Finance" }` renvoie tous les enregistrements pour le département des finances. Utilisez ce qui suit dans *function.json* :

```
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}"
    "connection": "CosmosDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>Exemple d’entrée avec plusieurs documents en C#

```csharp
public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
{   
    foreach (var doc in documents)
    {
        // operate on each document
    }    
}

public class QueuePayload
{
    public string departmentId { get; set; }
}
```

### <a name="input-sample-with-multiple-documents-in-javascript"></a>Exemple d’entrée avec plusieurs documents en JavaScript

```javascript
module.exports = function (context, input) {    
    var documents = context.bindings.documents;
    for (var i = 0; i < documents.length; i++) {
        var document = documents[i];
        // operate on each document
    }       
    context.done();
};
```

## <a id="docdboutput"></a>Liaison de sortie d’API DocumentDB
La liaison de sortie d’API DocumentDB vous permet d’écrire un nouveau document dans une base de données Azure Cosmos DB. Elle possède les propriétés suivantes dans *function.json* :

|Propriété  |Description  |
|---------|---------|
|**name**     | Nom du paramètre de liaison qui représente le document dans la fonction.  |
|**type**     | Doit être défini sur `documentdb`.        |
|**databaseName** | Base de données contenant la collection dans laquelle le document est créé.     |
|**collectionName**  | Nom de la collection dans laquelle le document est créé. |
|**createIfNotExists**     | Valeur booléenne indiquant si la collection doit être créée si elle n’existe pas déjà. La valeur par défaut est *false*. En effet, les collections sont créées avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**Connexion**     |Nom du paramètre d’application contenant votre chaîne de connexion Azure Cosmos DB.        |
|**direction**     | Doit être défini sur `out`.         |

## <a name="using-a-documentdb-api-output-binding"></a>Utilisation d’une liaison de sortie d’API DocumentDB
Cette section vous montre comment utiliser la liaison de sortie d’API DocumentDB dans le code de votre fonction.

Par défaut, lorsque vous écrivez dans le paramètre de sortie de votre fonction, un document est créé dans votre base de données. Ce document comporte un GUID généré automatiquement en tant qu’ID du document. Vous pouvez spécifier l’ID du document de sortie en spécifiant la propriété `id` dans l’objet JSON qui est passé au paramètre de sortie. 

>[!Note]  
>Lorsque vous spécifier l’ID d’un document existant, il est remplacé par le nouveau document de sortie. 

Pour générer plusieurs documents, vous pouvez également vous lier à `ICollector<T>` ou `IAsyncCollector<T>` où `T` est un des types pris en charge.

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>Exemple de liaison de sortie d’API DocumentDB
Supposons que le tableau `bindings` de function.json contient la liaison de sortie d’API DocumentDB suivante :

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_COSMOSDB",     
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

Et que vous souhaitez créer des documents Azure Cosmos DB au format suivant pour chaque enregistrement :

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
* [JavaScript](#outjavascript)

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

Cet exemple requiert un fichier `project.json` qui spécifie les dépendances NuGet `FSharp.Interop.Dynamic` et `Dynamitey` :

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>Exemple de sortie en JavaScript

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
