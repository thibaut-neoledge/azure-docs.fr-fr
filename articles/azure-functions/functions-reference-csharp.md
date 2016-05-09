<properties
	pageTitle="Information de référence pour les développeurs sur Azure Functions | Microsoft Azure"
	description="Découvrez comment développer sur Azure Functions à l’aide de C#."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="dotnet"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/14/2016"
	ms.author="chrande"/>

# Informations de référence pour les développeurs C# sur Azure Functions

L’expérience c# pour Azure Functions repose sur le Kit de développement logiciel (SDK) Azure WebJobs. Les données circulent dans votre fonction C# via des arguments de méthode. Les noms d’argument sont spécifiés dans `function.json`, et il existe des noms prédéfinis pour accéder à des éléments tels que l’enregistreur de fonctions et les jetons d’annulation.

Cet article repose sur l’hypothèse que vous avez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md).

## Fonctionnement de .csx

Le format `.csx` permet d’écrire moins « réutilisable » et de se contenter de la simple écriture d’une fonction C#. Pour Azure Functions, il suffit d’inclure les références d’assembly et les espaces de noms en haut, comme d’habitude, et au lieu d’encapsuler tous les éléments dans un espace de noms et une classe, vous pouvez simplement définir votre `Run` méthode. Si vous devez inclure toutes les classes, par exemple pour définir des objets POCO, vous pouvez inclure une classe dans le même fichier.

## Liaison aux arguments

Les diverses liaisons sont liées à une fonction C# via la propriété `name` de la configuration *function.json*. Chaque liaison possède ses propres types pris en charge documentés par liaison. Par exemple, un déclencheur d’objets blob peut prendre en charge une chaîne, un objet POCO ou plusieurs autres types. Vous pouvez utiliser le type qui répond le mieux à vos besoins.

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

## Journalisation

Pour consigner les résultats dans vos journaux de diffusion en continu en C#, vous pouvez inclure un argument typé `TraceWriter`. Nous vous recommandons de le nommer `log`. Nous vous recommandons d’éviter d’utiliser `Console.Write` dans Azure Functions.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
}
```

## Async

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

## Jeton d’annulation

Dans certains cas, certaines opérations peuvent être délicates à arrêter. Alors qu’il est toujours préférable d’écrire du code permettant de faire face à un blocage, définissez un argument typé [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) lorsque vous voulez traiter des demandes d’arrêt progressif. Un `CancellationToken` sera fourni si un arrêt de l’hôte est déclenché.

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

## Importation des espaces de noms

Utilisez la clause `using` si vous devez importer des espaces de noms.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Les espaces de noms suivants sont automatiquement importés et sont donc facultatifs :

* `System`
* `System.Collections.Generic`
* `System.Linq`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## Référencement des assemblys externes

Pour les assemblys de framework, ajoutez des références à l’aide de la directive `#r "AssemblyName"`.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Les assemblys suivants sont ajoutés automatiquement par l’environnement hébergeant Azure Functions :

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

En outre, les assemblys suivants ont une casse spécifique et peuvent être référencés par simplename (par exemple, `#r "AssemblyName"`) :

* `Newtonsoft.Json`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Si vous avez besoin de mentionner un assembly privé, vous pouvez charger le fichier d’assembly dans un dossier `bin` relatif à votre fonction et y faire référence à l’aide du nom de fichier (par exemple, `#r "MyAssembly.dll"`).

## Gestion des packages

Pour utiliser des packages NuGet dans une fonction C#, chargez un fichier *project.json* dans le dossier de fonction du système de fichiers du conteneur de fonctions. Voici un exemple de fichier *project.json* qui ajoute une référence à Microsoft.ProjectOxford.Face version 1.1.0 :

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

Lorsque vous chargez un fichier *project.json*, le runtime obtient les packages et ajoute automatiquement des références aux assemblys de packages. Vous n’êtes pas obligé d’ajouter de directives `#r "AssemblyName"`. Il suffit d’ajouter les instructions `using` requises à votre fichier *run.csx* pour utiliser les types définis dans les packages NuGet.

### Comment charger un fichier project.json

Commencez par vous assurer que votre conteneur de fonctions est en cours d’exécution. Ce que vous pouvez faire en ouvrant votre fonction dans le portail Azure. Il donne également accès aux journaux de diffusion en continu où le résultat de l’installation du package s’affiche.

Les conteneurs de fonctions sont créés sur App Service, de sorte que toutes les [options de déploiement disponibles sur les applications web standard](../app-service-web/web-sites-deploy.md) le sont également sur les conteneurs de fonctions. Voici les méthodes que vous pouvez utiliser.

#### Pour télécharger project.json à l’aide de Visual Studio Online (Monaco)

1. Dans le portail Azure Functions, cliquez sur **Paramètres du conteneur de fonctions**.

2. Dans la section **Paramètres avancés** cliquez sur **Accéder aux paramètres App Service**.

3. Cliquez sur **Outils**.

4. Sous **Développer**, cliquez sur **Visual Studio Online**.

5. **Activez-le** si ce n’est pas déjà pas déjà fait, puis cliquez sur **Accéder**.

6. Une fois Visual Studio Online chargé, glissez-déplacez votre fichier *project.json* dans le dossier de votre fonction (le dossier nommé après votre fonction).

#### Pour charger project.json à l’aide du point de terminaison SCM (Kudu) du conteneur de fonctions

1. Accédez à : `https://<function_app_name>.scm.azurewebsites.net`.

2. Cliquez sur **Console de débogage > CMD**.

3. Accédez à *D:\\home\\site\\wwwroot<nom\_fonction>*.

4. Glissez-déplacez votre fichier *project.json* dans le dossier (sur la grille de fichiers).

#### Pour charger project.json à l’aide de FTP

1. Suivez les instructions [ici](../app-service-web/web-sites-deploy.md#ftp) pour configurer le FTP.

2. Lorsque vous êtes connecté au site de l’application de fonction, copiez votre fichier *project.json* dans */site/wwwroot/<function_name>*.

#### Journal d’installation du package 

Une fois le fichier *project.json* chargé, un résultat ressemblant à l’exemple suivant s’affiche dans le journal de diffusion en continu de votre fonction :

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

## Réutilisation du code .csx

Vous pouvez utiliser des classes et des méthodes définies dans d’autres fichiers *.csx* dans votre fichier *run.csx*. Pour ce faire, utilisez les directives `#load` dans votre fichier *run.csx*, comme indiqué dans l’exemple suivant.

Exemple *run.csx* :

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemple *mylogger.csx* :

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

Vous pouvez utiliser un chemin d’accès relatif avec la directive `#load` :

* `#load "mylogger.csx"` charge un fichier situé dans le dossier de la fonction.

* `#load "loadedfiles\mylogger.csx"` charge un fichier situé dans un dossier du dossier de la fonction.

* `#load "..\shared\mylogger.csx"` charge un fichier situé dans un dossier situé au même niveau que le dossier de la fonction, c’est-à-dire, directement sous *wwwroot*.
 
La directive `#load` ne fonctionne qu’avec des fichiers *.csx* (script C#) et non avec des fichiers *.cs*.

## Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Informations de référence pour les développeurs NodeJS sur Azure Functions](functions-reference-node.md)
* [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0427_2016-->