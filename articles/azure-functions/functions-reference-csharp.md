---
title: "Information de référence pour les développeurs sur Azure Functions | Microsoft Docs"
description: "Découvrez comment développer sur Azure Functions à l’aide de C#."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: f28cda01-15f3-4047-83f3-e89d5728301c
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/13/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: da9da90e7ccd5d324b8f87a3585555ea5d5ed475
ms.openlocfilehash: d587ff744fea5393a34d5a576e6af32cac5d2b44


---
# <a name="azure-functions-c-developer-reference"></a>Informations de référence pour les développeurs C# sur Azure Functions
> [!div class="op_single_selector"]
> * [Script C#](functions-reference-csharp.md)
> * [Script F#](functions-reference-fsharp.md)
> * [Node.JS](functions-reference-node.md)
> 
> 

L’expérience c# pour Azure Functions repose sur le Kit de développement logiciel (SDK) Azure WebJobs. Les données circulent dans votre fonction C# via des arguments de méthode. Les noms d’argument sont spécifiés dans `function.json`, et il existe des noms prédéfinis pour accéder à des éléments tels que l’enregistreur de fonctions et les jetons d’annulation.

Cet article suppose que vous ayez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Fonctionnement de .csx
Le format `.csx` vous permet d’écrire de façon moins « réutilisable » et de vous concentrer uniquement sur l’écriture d’une fonction C#. Pour Azure Functions, il suffit d’inclure les références d’assembly et les espaces de noms en haut, comme d’habitude. Par ailleurs, au lieu d’encapsuler tous les éléments dans un espace de noms et une classe, vous pouvez simplement définir votre méthode `Run`. Si vous devez inclure toutes les classes, par exemple pour définir des objets OCT (objets CLR traditionnels), vous pouvez inclure une classe dans le même fichier.   

## <a name="binding-to-arguments"></a>Liaison aux arguments
Les diverses liaisons sont liées à une fonction C# par le biais de la propriété `name` de la configuration *function.json*. Chaque liaison possède ses propres types pris en charge documentés par liaison. Par exemple, un déclencheur d’objets blob peut prendre en charge une chaîne, un objet POCO ou plusieurs autres types. Vous pouvez utiliser le type qui répond le mieux à vos besoins. Des méthodes getter et setter doivent être définies pour chaque propriété d’un objet OCT. 

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## <a name="logging"></a>Journalisation
Pour consigner les résultats dans vos journaux de diffusion en continu en C#, vous pouvez inclure un argument typé `TraceWriter` . Nous vous recommandons de le nommer `log`. Nous vous recommandons d’éviter d’utiliser `Console.Write` dans Azure Functions.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>Async
Pour rendre une fonction asynchrone, utilisez le mot clé `async` et retournez un objet `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>Jeton d’annulation
Dans certains cas, certaines opérations peuvent être délicates à arrêter. Alors qu’il est toujours préférable d’écrire un code permettant de faire face à un blocage, vous définissez un argument typé [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) pour traiter des demandes d’arrêt progressif.  Un `CancellationToken` sera fourni si un arrêt de l’hôte est déclenché. 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="importing-namespaces"></a>Importation des espaces de noms
Si vous avez besoin d’importer des espaces de noms, vous pouvez le faire comme vous en avez l’habitude à l’aide de la clause `using` .

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Les espaces de noms suivants sont automatiquement importés et sont donc facultatifs :

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Référencement des assemblys externes
Pour les assemblys de framework, ajoutez des références à l’aide de la directive `#r "AssemblyName"` .

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Les assemblys suivants sont ajoutés automatiquement par l’environnement hébergeant Azure Functions :

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

En outre, les assemblys ci-après ont une casse spécifique et peuvent être référencés par leur nom simple (par exemple, `#r "AssemblyName"`) :

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

Si vous avez besoin de référencer un assembly privé, vous pouvez charger le fichier d’assembly dans un dossier `bin` relatif à votre fonction et le référencer à l’aide du nom de fichier (par exemple, `#r "MyAssembly.dll"`). Pour plus d’informations sur le téléchargement de fichiers vers votre conteneur de fonctions, consultez la section suivante sur la gestion des packages.

## <a name="package-management"></a>Gestion des packages
Pour utiliser des packages NuGet dans une fonction C#, chargez un fichier *project.json* dans le dossier de la fonction dans le système de fichiers du conteneur de fonctions. Voici un exemple de fichier *project.json* qui ajoute une référence à Microsoft.ProjectOxford.Face version 1.1.0 :

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Seul .NET Framework 4.6 est pris en charge. Par conséquent, assurez-vous que votre fichier *project.json* spécifie `net46` comme indiqué ici.

Lorsque vous chargez un fichier *project.json* , le runtime récupère les packages et ajoute automatiquement des références aux assemblys de packages. Vous n’avez pas besoin d’ajouter de directives `#r "AssemblyName"` . Il vous suffit d’ajouter les instructions `using` requises à votre fichier *run.csx* pour utiliser les types définis dans les packages NuGet.

### <a name="how-to-upload-a-projectjson-file"></a>Comment charger un fichier project.json
1. Commencez par vous assurer que votre conteneur de fonctions est en cours d’exécution. Ce que vous pouvez faire en ouvrant votre fonction dans le portail Azure. 
   
    Il donne également accès aux journaux de diffusion en continu où le résultat de l’installation du package s’affiche. 
2. Pour charger un fichier project.json, utilisez une des méthodes décrites dans la section **Comment mettre à jour les fichiers du conteneur de fonctions** de la rubrique [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md#fileupdate). 
3. Une fois le fichier *project.json* chargé, une sortie semblable à l’exemple ci-après s’affiche dans le journal de diffusion en continu de votre fonction :

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261 
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Variables d’environnement
Pour obtenir une variable d’environnement ou une valeur de paramètre d’application, utilisez `System.Environment.GetEnvironmentVariable`, comme illustré dans l’exemple de code suivant :

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " + 
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

## <a name="reusing-csx-code"></a>Réutilisation du code .csx
Vous pouvez utiliser des classes et des méthodes définies dans d’autres fichiers *.csx* au sein de votre fichier *run.csx*. Pour ce faire, utilisez les directives `#load` dans votre fichier *run.csx*. Dans l’exemple suivant, une routine de journalisation nommée `MyLogger` est partagée dans *myLogger.csx* et chargés dans *run.csx* à l’aide de la directive`#load` : 

Exemple *run.csx*:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemple *mylogger.csx*:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

L’utilisation d’un fichier *.csx* partagé est un cas de figure courant lorsque vous souhaitez typer fortement vos arguments entre les fonctions à l’aide d’un objet OCT. Dans l’exemple simplifié suivant, un déclencheur HTTP et un déclencheur de file d’attente partagent un objet OCT nommé `Order` pour typer fortement les données de commande :

Exemple avec *run.csx* pour un déclencheur HTTP :

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Exemple avec *run.csx* pour un déclencheur de file d’attente :

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Exemple avec *order.csx* : 

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId + 
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

Vous pouvez utiliser un chemin d’accès relatif avec la directive `#load` :

* `#load "mylogger.csx"` charge un fichier situé dans le dossier de la fonction.
* `#load "loadedfiles\mylogger.csx"` charge un fichier situé dans un dossier du dossier de la fonction.
* `#load "..\shared\mylogger.csx"` charge un fichier situé dans un dossier situé au même niveau que le dossier de la fonction, c’est-à-dire directement sous *wwwroot*.

La directive `#load` ne fonctionne qu’avec des fichiers *.csx* (script C#) et non avec des fichiers *.cs*. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Référence du développeur Azure Functions](functions-reference.md)
* [Informations de référence pour les développeurs F# sur Azure Functions](functions-reference-fsharp.md)
* [Azure Functions NodeJS developer reference (Référence pour les développeurs NodeJS Azure Functions)](functions-reference-node.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)




<!--HONumber=Nov16_HO3-->


