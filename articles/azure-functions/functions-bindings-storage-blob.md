---
title: "Liaisons de Stockage Blob d’Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs et des liaisons Stockage Azure dans Azure Functions."
services: functions
documentationcenter: na
author: lindydonna
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
ms.date: 05/25/2017
ms.author: donnam, glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b819bf4461f14033dd2c00331e3c3e4d0fbafde6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="azure-functions-blob-storage-bindings"></a>Liaisons de stockage Blob Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et utiliser des liaisons Azure Stockage Blob dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur, d’entrée et de sortie pour Azure Stockage Blob. Pour les fonctionnalités qui sont disponibles dans toutes les liaisons, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Un [compte de stockage pour objet blob uniquement](../storage/storage-create-storage-account.md#blob-storage-accounts) n’est pas pris en charge. Les déclencheurs et les liaisons de stockage blob nécessitent un compte de stockage à usage général. 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>Déclencheurs et liaisons de stockage blob

Avec le déclencheur Stockage Blob Azure, le code de votre fonction est appelé quand un objet blob nouveau ou mis à jour est détecté. Le contenu de l’objet blob est fourni comme entrée de la fonction.

Définissez un déclencheur de stockage blob en utilisant l’onglet **Intégrer** dans le portail Functions. Le portail crée la définition suivante dans la section **bindings** de *function.json* :

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

Les liaisons d’entrée et de sortie d’objet blob sont définies en utilisant `blob` comme type de liaison :

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* La propriété `path` prend en charge les expressions de liaison et les paramètres de filtre. Consultez [Modèles de nom](#pattern).
* La propriété `connection` doit contenir le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. Dans le portail Azure, l’éditeur standard de l’onglet **Intégrer** configure ce paramètre d’application pour vous quand vous sélectionnez un compte de stockage.

> [!NOTE]
> Quand vous utilisez un déclencheur d’objet blob dans un plan Consommation, il peut y avoir jusqu’à 10 minutes de délai dans le traitement des nouveaux objets blob après qu’une application de fonction est devenue inactive. Une fois l’application de fonction en cours d’exécution, les objets blob sont traités immédiatement. Pour éviter ce délai initial, pensez à l’une des options suivantes :
> - Utilisez un plan App Service avec le paramètre Toujours actif activé.
> - Utilisez un autre mécanisme pour déclencher le traitement de l’objet blob, comme un message de file d’attente qui contient le nom de l’objet blob. Pour un exemple, consultez [Déclencheur de file d’attente avec liaison d’entrée d’objet blob](#input-sample).

<a name="pattern"></a>

### <a name="name-patterns"></a>Modèles de nom
Vous pouvez spécifier un modèle de nom d’objet blob dans la propriété `path`, qui peut être une expression de filtre ou de liaison. Consultez [Expressions et modèles de liaison](functions-triggers-bindings.md#binding-expressions-and-patterns).

Par exemple, pour filtrer des objets blob qui commencent par la chaîne « original », utilisez la définition suivante. Ce chemin trouve un objet blob appelé *original-Blob1.txt* dans le conteneur *input*, et la valeur de la variable `name` dans le code de la fonction est `Blob1`.

```json
"path": "input/original-{name}",
```

Pour se lier séparément au nom de fichier et à l’extension de l’objet blob, utilisez deux modèles. Ce chemin trouve également un objet blob nommé *original-Blob1.txt*, et les variables `blobname` et `blobextension` du code de la fonction sont *original-Blob1* et *txt*.

```json
"path": "input/{blobname}.{blobextension}",
```

Vous pouvez restreindre le type de fichier d’objets blob à l’aide d’une valeur fixe pour l’extension de fichier. Par exemple, pour déclencher uniquement sur des fichiers .png, utilisez le modèle suivant :

```json
"path": "samples/{name}.png",
```

Les accolades sont des caractères spéciaux dans les modèles de nom. Pour spécifier des noms d’objet blob dont le nom contient des accolades, utilisez une séquence d’échappement sous la forme de deux accolades. L’exemple suivante trouve un objet blob nommé *{20140101}-soundfile.mp3* dans le conteneur *images*, et la valeur de la variable `name` dans le code de la fonction est *soundfile.mp3*. 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>Métadonnées d’un déclencheur

Le déclencheur d’objet blob fournit plusieurs propriétés de métadonnées. Ces propriétés peuvent être utilisées dans des expressions de liaison dans d’autres liaisons ou en tant que paramètres dans votre code. Ces valeurs ont la même sémantique que [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

- **BlobTrigger**. Saisissez `string`. Chemin de l’objet blob déclencheur
- **Uri**. Saisissez `System.Uri`. URI de l’objet blob pour l’emplacement principal.
- **Properties**. Saisissez `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`. Propriétés système de l’objet blob.
- **Metadata**. Saisissez `IDictionary<string,string>`. Métadonnées définies par l’utilisateur pour l’objet blob.

<a name="receipts"></a>

### <a name="blob-receipts"></a>Reçus d’objets blob
Le runtime Azure Functions vérifie qu’aucune fonction de déclencheur d’objet blob n’est appelée plusieurs fois pour un même objet blob, nouveau ou mis à jour. Pour déterminer si la version d’un objet blob donné a été traitée, il gère des *reçus d’objet blob*.

Azure Functions stocke les reçus d’objet blob dans un conteneur appelé *azure-webjobs-hosts* dans le compte de stockage Azure de votre application de fonction (définie par le paramètre d’application `AzureWebJobsStorage`). Un reçu d’objet blob contient les informations suivantes :

* Fonction déclenchée (« *&lt;nom de l’application de fonction>*.Functions.*&lt;nom de la fonction>* », par exemple : « MyFunctionApp.Functions.CopyBlob »)
* Nom du conteneur
* Type d’objet blob (« BlockBlob » ou « PageBlob »)
* Nom de l’objet blob
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

Pour forcer le retraitement d’un objet blob, supprimez manuellement le reçu de l’objet blob du conteneur *azure-webjobs-hosts*.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Gestion des objets blob incohérents
En cas d’échec d’une fonction de déclencheur d’objet blob, Azure Functions réessaie cette fonction jusqu’à 5 fois par défaut. 

Si les 5 tentatives échouent, Azure Functions ajoute un message à une file d’attente de stockage nommée *webjobs-blobtrigger-poison*. Le message en file d’attente associé aux objets blob incohérents correspond à un objet JSON, qui contient les propriétés suivantes :

* FunctionId (au format *&lt;nom de l’application de fonction>*.Functions.*&lt;nom de la fonction>*)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

### <a name="blob-polling-for-large-containers"></a>Interrogation de blob pour les grands conteneurs
Si le conteneur d’objets blob surveillé contient plus de 10 000 objets blob, le runtime Functions recherche les objets blob nouveaux ou modifiés dans les fichiers journaux. Ce processus ne se déroule pas en temps réel. Il se peut qu’une fonction ne se déclenche que quelques minutes ou plus après la création de l’objet blob. En outre, les [journaux de stockage sont créés selon le principe du meilleur effort](/rest/api/storageservices/About-Storage-Analytics-Logging). Il n’existe aucune garantie que tous les événements sont capturés. Dans certaines conditions, des journaux peuvent être omis. Si vous avez besoin de traitement d’objets blob plus rapide ou plus fiable, envisagez de créer un [message de file d’attente](../storage/storage-dotnet-how-to-use-queues.md)  
 quand vous créez l’objet blob. Ensuite, utilisez un [déclencheur de file d’attente](functions-bindings-storage-queue.md) au lieu d’un déclencheur d’objet blob pour traiter l’objet blob.

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>Utilisation d’un déclencheur d’objets blob et d’une liaison d’entrée
Dans les fonctions .NET, accédez aux données des objets blob en utilisant un paramètre de méthode comme `Stream paramName`. Ici, `paramName` est la valeur que vous avez spécifiée dans la [configuration du déclencheur](#trigger). Dans les fonctions Node.js, accédez aux données de l’objet blob d’entrée en utilisant `context.bindings.<name>`.

Dans .NET, vous pouvez lier aux types de la liste ci-dessous. S’ils sont utilisés comme liaison d’entrée, certains de ces types nécessitent une direction de liaison `inout` dans *function.json*. Cette direction n’est pas prise en charge par l’éditeur standard : vous devez donc utiliser l’éditeur avancé.

* `TextReader`
* `Stream`
* `ICloudBlob` (nécessite la direction de liaison « inout »)
* `CloudBlockBlob` (nécessite la direction de liaison « inout »)
* `CloudPageBlob` (nécessite la direction de liaison « inout »)
* `CloudAppendBlob` (nécessite la direction de liaison « inout »)

Si des objets blob de texte sont attendus, vous pouvez également lier à un type `string` .NET. Ceci est recommandé uniquement si la taille de l’objet blob est petite, car tout le contenu de l’objet blob est chargé en mémoire. En général, il est préférable d’utiliser un type `Stream` ou `CloudBlockBlob`.

## <a name="trigger-sample"></a>Exemple de déclencheur
Supposons le code function.json suivant, qui définit un déclencheur de stockage d’objet blob :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

Consultez l’exemple dans le langage de votre choix pour voir comment enregistrer le contenu de chaque objet blob ajouté au conteneur surveillé.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>Exemples de déclencheur d’objet blob en C# #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Exemple de déclencheur en Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a> <a name=storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>Utilisation d’une liaison de sortie d’objet blob

Dans les fonctions .NET, vous devez utiliser un paramètre `out string` dans la signature de votre fonction ou un des types de la liste suivante. Dans les fonctions Node.js, vous accédez à l’objet blob de sortie en utilisant `context.bindings.<name>`.

Dans les fonctions .NET, vous pouvez définir une sortie vers les types suivants :

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>Exemple de déclencheur de file d’attente avec une entrée et une sortie d’objet blob
Supposons le code function.json suivant, qui définit un [déclencheur de stockage de file d’attente](functions-bindings-storage-queue.md), une entrée de stockage d’objet blob et une sortie de stockage d’objet blob. Notez l’utilisation de la propriété de métadonnées `queueTrigger`. dans les propriétés `path` de l’entrée et de la sortie d’objet blob :

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

### <a name="blob-binding-example-in-c"></a>Exemple de liaison d’objet blob en C# #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Exemple de liaison d’objet blob en Node.js

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


