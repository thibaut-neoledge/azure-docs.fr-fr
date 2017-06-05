---
title: "Liaisons d’objets blob Stockage Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs et des liaisons Azure Storage dans Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: chrande, glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 198a8421636945bdf60c4ed519d065617a7fc287
ms.contentlocale: fr-fr
ms.lasthandoff: 05/15/2017


---
# <a name="azure-functions-blob-storage-bindings"></a>Liaisons de Stockage Blob Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des liaisons d’objets blob Stockage Azure dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour les objets blob Stockage Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Un [compte de stockage pour objet blob uniquement](../storage/storage-create-storage-account.md#blob-storage-accounts) n’est pas pris en charge. Azure Functions requiert un compte de stockage polyvalent à utiliser avec des objets blob. 
> 
> 

<a name="trigger"></a>

## <a name="storage-blob-trigger"></a>Déclencheur d’objet blob Stockage
Le déclencheur d’objet blob Azure Storage vous permet de surveiller un conteneur de stockage pour les nouveaux objets blob et ceux mis à jour, mais aussi d’exécuter votre code de fonction en cas de détection de modifications. 

Le déclencheur d’objet blob Stockage d’une fonction utilise les objets JSON suivants dans le tableau `bindings` de function.json :

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection":"<Name of app setting - see below>"
}
```

Notez les points suivants :

* Pour `path`, consultez [Modèles de nom](#pattern) pour savoir comment mettre en forme les modèles de nom d’objet blob.
* `connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le Portail Azure, l’éditeur standard sous l’onglet **Intégrer** configure ce paramètre d’application pour vous quand vous créez un compte de stockage ou en sélectionnez un. Pour créer manuellement ce paramètre d’application, consultez [Configurer ce paramètre d’application manuellement](functions-how-to-use-azure-function-app-settings.md). 

Lors de l’exécution sur un plan de consommation, si une Function App est devenue inactive, le traitement des nouveaux objets blob peut prendre jusqu’à 10 minutes. Une fois la Function App en cours d’exécution, les objets blob sont traités plus rapidement. Pour éviter ce délai initial, utilisez un plan App Service régulier avec Toujours actif activé ou un autre mécanisme pour déclencher le traitement des objets blob, par exemple un message de file d’attente contenant le nom de l’objet blob. 

En outre, consultez les sections suivantes pour plus d’informations :

* [Modèles de nom](#pattern)
* [Reçus d’objets blob](#receipts)
* [Gestion des objets blob incohérents](#poison)

<a name="pattern"></a>

### <a name="name-patterns"></a>Modèles de nom
Vous pouvez spécifier un modèle de nom d’objet blob dans la propriété `path` . Par exemple :

```json
"path": "input/original-{name}",
```

Ce chemin d’accès trouverait un objet blob appelé *original-Blob1.txt* dans le conteneur *input*, et la variable `name` du code de fonction présenterait la valeur `Blob1`.

Autre exemple :

```json
"path": "input/{blobname}.{blobextension}",
```

Ce chemin d’accès trouverait également un objet blob nommé *original-Blob1.txt*, et les variables `blobname` et `blobextension` du code de fonction présenteraient respectivement les valeurs *original-Blob1* et *txt*.

Vous pouvez restreindre le type de fichier d’objets blob à l’aide d’une valeur fixe pour l’extension de fichier. Par exemple :

```json
"path": "samples/{name}.png",
```

Dans ce cas, seuls les objets blob *.png* dans le conteneur *samples* déclenchent la fonction.

Les accolades sont des caractères spéciaux dans les modèles de nom. Pour spécifier des noms d’objet blob qui présentent des accolades, doublez ces dernières. Par exemple :

```json
"path": "images/{{20140101}}-{name}",
```

Ce chemin trouverait un objet blob nommé *{20140101}-soundfile.mp3* dans le conteneur *images*, et la valeur de la variable `name` dans le code de fonction serait *soundfile.mp3*. 

<a name="receipts"></a>

### <a name="blob-receipts"></a>Reçus d’objets blob
Le runtime Azure Functions vérifie qu’aucune fonction de déclencheur d’objet blob n’est appelée plusieurs fois pour un même objet blob, nouveau ou mis à jour. Pour ce faire, il tient à jour les *reçus d’objets blob* pour déterminer si la version d’un objet blob donné a été traitée.

Les reçus d’objets blob sont stockés dans un conteneur appelé *azure-webjobs-hosts* associé au compte de stockage Azure pour votre application de fonction (indiqué par le paramètre d’application `AzureWebJobsStorage`). Un reçu d’objet blob contient les informations suivantes :

* Fonction déclenchée ("*&lt;nom de l’application de fonction>*.Functions.*&lt;nom de la fonction>*", par exemple : "functionsf74b96f7.Functions.CopyBlob")
* Nom du conteneur
* Type d’objet blob (« BlockBlob » ou « PageBlob »)
* Nom de l’objet blob
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

Pour forcer le retraitement d’un objet blob, supprimez manuellement le reçu de l’objet blob du conteneur *azure-webjobs-hosts*.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Gestion des objets blob incohérents
En cas d’échec d’une fonction de déclenchement d’objet blob, Azure Functions réessaie cette fonction jusqu’à 5 fois par défaut (première tentative comprise) pour un objet blob donné. Si toutes les 5 tentatives échouent, Functions ajoute un message à une file d’attente Stockage nommée *webjobs-blobtrigger-poison*. Le message en file d’attente associé aux objets blob incohérents correspond à un objet JSON, qui contient les propriétés suivantes :

* FunctionId (au format *&lt;nom de l’application de fonctions>*.Functions.*&lt;nom de la fonction>*)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

### <a name="blob-polling-for-large-containers"></a>Interrogation de blob pour les grands conteneurs
Si le conteneur d’objets blob surveillé par la liaison contient plus de 10 000 objets blob, le runtime Functions analyse les fichiers journaux pour effectuer le suivi des objets blob nouveaux ou modifiés. Ce processus ne se déroule pas en temps réel. Il se peut qu’une fonction ne se déclenche que quelques minutes ou plus après la création de l’objet blob. En outre, les [journaux de stockage sont créés selon le principe du meilleur effort](https://msdn.microsoft.com/library/azure/hh343262.aspx). Il n’existe aucune garantie que tous les événements sont capturés. Dans certaines conditions, des journaux peuvent être omis. Si les limitations relatives à la vitesse et à la fiabilité des déclencheurs d’objet blob pour les conteneurs volumineux ne sont pas acceptables pour votre application, nous vous recommandons de créer un [message de file d’attente](../storage/storage-dotnet-how-to-use-queues.md) quand vous créez l’objet blob, et d’utiliser un [déclencheur de file d’attente](functions-bindings-storage-queue.md) plutôt qu’un déclencheur d’objet blob pour traiter cet objet blob.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utilisation du déclencheur
Dans les fonctions C#, vous liez les données d’objet blob d’entrée à l’aide d’un paramètre nommé dans la signature de la fonction, comme `<T> <name>`.
Où `T` est le type de données dans lequel vous souhaitez désérialiser les données, et `paramName` le nom que vous avez spécifié dans le [code JSON du déclencheur](#trigger). Dans les fonctions Node.js, vous accédez aux données d’objet blob d’entrée en utilisant `context.bindings.<name>`.

L’objet blob peut être désérialisé dans l’un des types suivants :

* N’importe quel [objet](https://msdn.microsoft.com/library/system.object.aspx) : utile pour les données d’objet blob sérialisées en JSON.
  Si vous déclarez un type d’entrée personnalisé (par exemple, `FooType`), Azure Functions tente de désérialiser les données JSON dans le type spécifié.
* Chaîne : utile pour les données d’objet blob de texte.

Dans les fonctions C#, vous pouvez également effectuer une liaison vers un des types suivants (le runtime Functions tente de désérialiser les données d’objet blob à l’aide de ce type) :

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* Autres types de désérialisation par [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

## <a name="trigger-sample"></a>Exemple de déclencheur
Supposons le code function.json suivant, qui définit un déclencheur d’objet blob Stockage :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

Consultez l’exemple dans le langage de votre choix pour voir comment enregistrer le contenu de chaque objet blob ajouté au conteneur surveillé.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Utilisation du déclencheur en C# #

```cs
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

``` 
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Utilisation du déclencheur en Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

<a name="input"></a>

## <a name="storage-blob-input-binding"></a>Liaison d’entrée d’objets blob Stockage
La liaison d’entrée d’objet blob Stockage Azure vous permet d’utiliser un objet blob à partir d’un conteneur de stockage dans votre fonction. 

L’entrée d’objet blob Stockage d’une fonction utilise les objets JSON suivants dans le tableau `bindings` de function.json :

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "blob",
  "direction": "in"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

Notez les points suivants :

* `path` doit contenir le nom du conteneur et le nom de l’objet blob. Par exemple, si votre fonction contient un [déclencheur de file d’attente](functions-bindings-storage-queue.md), vous pouvez utiliser `"path": "samples-workitems/{queueTrigger}"` pour pointer vers un objet blob dans le conteneur `samples-workitems` avec un nom qui correspond au nom de l’objet blob spécifié dans le message du déclencheur.   
* `connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le portail Azure, l’éditeur standard sous l’onglet **Intégrer** configure ce paramètre d’application pour vous quand vous créez un compte Stockage ou en sélectionne un. Pour créer manuellement ce paramètre d’application, consultez [Configurer ce paramètre d’application manuellement](functions-how-to-use-azure-function-app-settings.md). 

<a name="inputusage"></a>

## <a name="input-usage"></a>Utilisation en entrée
Dans les fonctions C#, vous liez les données d’objet blob d’entrée à l’aide d’un paramètre nommé dans la signature de la fonction, comme `<T> <name>`.
Où `T` est le type de données dans lequel vous souhaitez désérialiser les données, et `paramName` le nom que vous avez spécifié dans la [liaison d’entrée](#input). Dans les fonctions Node.js, vous accédez aux données d’objet blob d’entrée en utilisant `context.bindings.<name>`.

L’objet blob peut être désérialisé dans l’un des types suivants :

* N’importe quel [objet](https://msdn.microsoft.com/library/system.object.aspx) : utile pour les données d’objet blob sérialisées en JSON.
  Si vous déclarez un type d’entrée personnalisé (par exemple, `InputType`), Azure Functions tente de désérialiser les données JSON dans le type spécifié.
* Chaîne : utile pour les données d’objet blob de texte.

Dans les fonctions C#, vous pouvez également effectuer une liaison vers un des types suivants (le runtime Functions tente de désérialiser les données d’objet blob à l’aide de ce type) :

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="inputsample"></a>

## <a name="input-sample"></a>Exemple d’entrée
Supposons le code function.json suivant, qui définit un [déclencheur de file d’attente Stockage](functions-bindings-storage-queue.md), une entrée d’objet blob Stockage et une sortie d’objet blob Stockage :

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Consultez l’exemple dans le langage de votre choix pour voir comment copier l’objet blob d’entrée dans l’objet blob de sortie.

* [C#](#incsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>

### <a name="input-usage-in-c"></a>Utilisation en entrée en C# #

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

``` 
-->

<a name="innodejs"></a>

### <a name="input-usage-in-nodejs"></a>Utilisation en entrée en Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

<a name="output"></a>

## <a name="storage-blob-output-binding"></a>Liaison de sortie d’objet blob Stockage
La liaison de sortie d’objet blob Stockage Azure vous permet d’écrire des objets blob dans un conteneur Stockage dans votre fonction. 

La sortie d’objet blob Stockage pour une fonction utilise les objets JSON suivants dans le tableau `bindings` de function.json :

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "blob",
  "direction": "out",
  "path": "<Path of input blob - see below>",
  "connection": "<Name of app setting - see below>"
}
```

Notez les points suivants :

* `path` doit contenir le nom du conteneur et le nom de l’objet blob destinataires de l’opération d’écriture. Par exemple, si votre fonction contient un [déclencheur de file d’attente](functions-bindings-storage-queue.md), vous pouvez utiliser `"path": "samples-workitems/{queueTrigger}"` pour pointer vers un objet blob dans le conteneur `samples-workitems` avec un nom qui correspond au nom de l’objet blob spécifié dans le message du déclencheur.   
* `connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le Portail Azure, l’éditeur standard sous l’onglet **Intégrer** configure ce paramètre d’application pour vous quand vous créez un compte de stockage ou en sélectionnez un. Pour créer manuellement ce paramètre d’application, consultez [Configurer ce paramètre d’application manuellement](functions-how-to-use-azure-function-app-settings.md). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Utilisation en sortie
Dans les fonctions C#, vous liez l’objet blob de sortie à l’aide du paramètre `out` nommé dans la signature de la fonction, par exemple, `out <T> <name>`, où `T` est le type de données dans lequel vous souhaitez sérialiser les données, et `paramName` le nom que vous avez spécifié dans la [liaison de sortie](#output). Dans les fonctions Node.js, vous accédez à l’objet blob de sortie en utilisant `context.bindings.<name>`.

Vous pouvez écrire dans l’objet blob de sortie à l’aide d’un des types suivants :

* N’importe quel [objet](https://msdn.microsoft.com/library/system.object.aspx) : utile pour la sérialisation JSON.
  Si vous déclarez un type de sortie personnalisée (par exemple, `out OutputType paramName`), Azure Functions tente de sérialiser un objet en JSON. Si le paramètre de sortie est Null quand la fonction s’arrête, le runtime Functions crée un objet blob comme objet Null.
* Chaîne : (`out string paramName`) utile pour les données d’objet blob de texte. Le runtime Functions crée un objet blob uniquement si le paramètre de chaîne n’est pas Null quand la fonction s’arrête.

Dans les fonctions C#, vous pouvez également définir une sortie vers les types suivants :

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="outputsample"></a>

## <a name="output-sample"></a>Exemple de sortie
Voir [Exemple d’entrée](#inputsample).

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


