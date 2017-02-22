---
title: Liaisons de table Azure Functions Storage | Microsoft Docs
description: "Découvrez comment utiliser des déclencheurs et des liaisons Azure Storage dans Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 0d37eb09a6c8a0bb39a331e51a8993c114202b91
ms.openlocfilehash: 88858cffa5ddc6ba83152d3430f5400a1c66a26a


---
# <a name="azure-functions-storage-table-bindings"></a>Liaisons de table Azure Functions Storage
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des déclencheurs et des liaisons de table Azure Storage dans Azure Functions. Azure Functions prend en charge les liaisons d’entrée et de sortie pour les tables Azure Storage.

La liaison de table Storage prend en charge les scénarios suivants :

* **Lecture d’une seule ligne dans une fonction C# ou Node.js** - Définition de `partitionKey` et `rowKey`. Les propriétés `filter` et `take` ne sont pas utilisées dans ce scénario.
* **Lecture de plusieurs lignes dans une fonction C#** - Le runtime Functions fournit un objet `IQueryable<T>` lié à la table. Le type `T` doit dériver de `TableEntity` ou implémenter `ITableEntity`. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario ; s’il y a lieu, vous pouvez utiliser l’objet `IQueryable` pour effectuer le filtrage éventuellement requis. 
* **Lecture de plusieurs lignes dans une fonction Node** - Définition des propriétés `filter` et `take`. Ne définissez pas `partitionKey` ni `rowKey`.
* **Écriture d'une ou plusieurs lignes dans une fonction C#** Le runtime Functions fournit un objet `ICollector<T>` ou `IAsyncCollector<T>` lié à la table, où `T` spécifie le schéma des entités à ajouter. En général, le type `T` dérive de `TableEntity` ou implémente `ITableEntity`, mais ce n’est pas obligatoire. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Liaison d’entrée d'une table Storage
La liaison d’entrée d'une table Azure Storage vous permet d’utiliser une table Storage dans votre fonction. 

L’entrée de table Storage d’une fonction utilise les objets JSON suivants dans le tableau `bindings` de function.json :

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

Notez les points suivants : 

* Utilisez `partitionKey` et `rowKey` pour lire une seule entité. Ces propriétés sont facultatives. 
* `connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le portail Azure, l’éditeur standard sous l’onglet **Intégrer** configure ce paramètre d’application pour vous quand vous créez un compte Stockage ou en sélectionne un. Vous pouvez également [configurer ce paramètre d’application manuellement](functions-how-to-use-azure-function-app-settings.md#application-settings).  

<a name="inputusage"></a>

## <a name="input-usage"></a>Utilisation en entrée
Dans les fonctions C#, vous liez l'entité (ou les entités) de table d’entrée à l’aide d’un paramètre nommé dans la signature de la fonction, comme `<T> <name>`.
Où `T` est le type de données dans lequel vous souhaitez désérialiser les données, et `paramName` le nom que vous avez spécifié dans la [liaison d’entrée](#input). Dans les fonctions Node.js, vous accédez à l'entité (ou les entités) de table d’entrée à l’aide de `context.bindings.<name>`.

Les données d’entrée peuvent être désérialisées dans les fonctions Node.js ou C#. Les objets désérialisés ont des propriétés `RowKey` et `PartitionKey`.

Dans les fonctions C#, vous pouvez également effectuer une liaison vers un des types suivants (le runtime Functions tente de désérialiser les données de la table à l’aide de ce type) :

* Tout type qui implémente `ITableEntity`
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>Exemple d’entrée
Supposons que vous avez le fichier function.json suivant, qui utilise un déclencheur de file d’attente pour lire une seule ligne du tableau. Le JSON spécifie `PartitionKey` 
`RowKey`. `"rowKey": "{queueTrigger}"` indique que la clé de ligne provient de la chaîne de message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Consultez l’exemple spécifique au langage, qui lit une entité de table unique.

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.JS](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Exemple d’entrée en C# #
```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>Exemple d’entrée en F# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Exemple d’entrée en Node.js
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Liaison de sortie d'une table Storage
La liaison de sortie de table Azure Storage vous permet d’écrire des entités dans une table Storage de votre fonction. 

La sortie de table Storage pour une fonction utilise les objets JSON suivants dans le tableau `bindings` de function.json :

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

Notez les points suivants : 

* Utilisez `partitionKey` et `rowKey` pour écire une seule entité. Ces propriétés sont facultatives. Vous pouvez également spécifier `PartitionKey` et `RowKey` lorsque vous créez les objets d’entité dans votre code de fonction.
* `connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le portail Azure, l’éditeur standard sous l’onglet **Intégrer** configure ce paramètre d’application pour vous quand vous créez un compte Stockage ou en sélectionne un. Vous pouvez également [configurer ce paramètre d’application manuellement](functions-how-to-use-azure-function-app-settings.md#application-settings). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilisation en sortie
Dans les fonctions C#, vous liez la sortie de table à l’aide du paramètre `out` nommé dans la signature de la fonction, par exemple, `out <T> <name>`, où `T` est le type de données dans lequel vous souhaitez sérialiser les données, et `paramName` le nom que vous avez spécifié dans la [liaison de sortie](#output). Dans les fonctions Node.js, vous accédez à la sortie de table en utilisant `context.bindings.<name>`.

Vous pouvez sérialiser les objets dans les fonctions Node.js ou C#. Dans les fonctions C#, vous pouvez également lier les types suivants :

* Tout type qui implémente `ITableEntity`
* `ICollector<T>` (pour la sortie de plusieurs entités. Voir l'[exemple](#outcsharp).)
* `IAsyncCollector<T>` (version asynchrone de `ICollector<T>`)
* `CloudTable` (utilisation du Kit de développement logiciel (SDK) de stockage Azure. Voir l'[exemple](#readmulti).)

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemple de sortie
Les exemples de fichiers *function.json* et *run.csx* ci-après indiquent comment écrire plusieurs entités de table.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Consultez l’exemple spécifique au langage, qui crée plusieurs entités de table.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Exemple de sortie en C# #
```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Exemple de sortie en F# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Exemple de sortie en Node.js
```javascript
module.exports = function (context) {

    context.bindings.outputTable = [];

    for (i = 1; i < 10; i++) {
        context.bindings.outputTable.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });

    context.done();
};
```

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>Exemple : lire plusieurs entités de table en C##  #
L’exemple de fichier *function.json* et de code C# ci-après lit les entités relatives à une clé de partition spécifiée dans le message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Le code C# ajoute une référence au SDK Azure Storage afin que le type d’entité puisse dériver de `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Feb17_HO3-->


