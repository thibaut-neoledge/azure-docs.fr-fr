<properties
	pageTitle="Déclencheurs et liaisons Azure Functions | Microsoft Azure"
	description="Découvrez comment utiliser des déclencheurs et des liaisons dans Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Informations de référence pour les développeurs sur les déclencheurs et liaisons Azure Functions

Cet article explique comment configurer et coder des déclencheurs et des liaisons dans Azure Functions. La plupart de ces liaisons sont aisément gérées par le biais de l’interface utilisateur **Intégrer** du portail Azure, mais ce dernier ne décrit pas toutes les fonctionnalités et options de chaque liaison.

Cet article repose sur l’hypothèse que vous avez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md), ainsi que les articles de référence pour les développeurs concernant [C#](functions-reference-csharp.md) ou [Node](functions-reference-node.md).

## Déclencheurs et liaisons HTTP et WebHook

Vous pouvez utiliser un déclencheur HTTP ou WebHook pour appeler une fonction en réponse à une requête HTTP. La requête doit inclure une clé API, qui n’est actuellement disponible que dans l’interface utilisateur du portail Azure.

L’URL de fonction est une combinaison de l’URL du conteneur de fonctions et du nom de fonction :

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

Le fichier *function.json* fournit les propriétés qui se rapportent à la requête et à la réponse HTTP.

Propriétés de la requête HTTP :

- `name` : nom de variable utilisé dans le code de fonction pour l’objet de requête (ou pour le corps de requête dans le cas des fonctions Node.js).
- `type` : propriété devant être définie sur *httpTrigger*.
- `direction` : propriété devant être définie sur *in*. 
- `webHookType` : pour les déclencheurs WebHook, les valeurs valides sont *github*, *slack* et *genericJson*. Pour un déclencheur HTTP autre qu’un WebHook, définissez cette propriété sur une chaîne vide. Pour plus d’informations sur les WebHooks, voir la section ci-après, [Déclencheurs WebHook](#webhook-triggers).
- `authLevel` : ne s’applique pas aux déclencheurs WebHook. Définissez cette propriété sur "function" pour demander la clé API, sur "anonymous" pour annuler l’exigence de clé API ou sur "admin" pour exiger la clé API principale. Pour plus d’informations, voir la section [Clés API](#apikeys) ci-dessous.

Propriétés de la réponse HTTP :

- `name` : nom de variable utilisé dans le code de fonction pour l’objet de réponse.
- `type` : propriété devant être définie sur *http*.
- `direction` : propriété devant être définie sur *out*. 
 
Exemple de fichier *function.json* :

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

### Déclencheurs WebHook

Un déclencheur WebHook est un déclencheur HTTP qui présente les caractéristiques ci-après conçues pour les WebHooks :

* Pour les fournisseurs de WebHooks spécifiques (les fournisseurs actuellement pris en charge sont GitHub et Slack), le runtime Functions valide la signature du fournisseur.
* Dans le cas des fonctions Node.js, le runtime Functions fournit le corps de requête plutôt que l’objet de requête. Les fonctions C# ne font l’objet d’aucune gestion spéciale, car vous contrôlez les éléments fournis en spécifiant le type de paramètre. Si vous spécifiez `HttpRequestMessage`, vous obtenez l’objet de requête. Si vous spécifiez un type POCO, le runtime Functions essaie d’analyser un objet JSON dans le corps de la requête pour spécifier les propriétés de l’objet.
* Pour déclencher une fonction WebHook, la requête HTTP doit inclure une clé API. Pour les déclencheurs HTTP non-WebHook, cette exigence est facultative.

Pour plus d’informations sur la configuration d’un WebHook GitHub, voir [GitHub Developer - Creating WebHooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) (page « Création de WebHooks » du site GitHub Developer).

### Clés API

Par défaut, une clé API doit être fournie avec une requête HTTP pour déclencher une fonction HTTP ou WebHook. Cette clé peut être incluse dans une variable de chaîne de requête nommée `code` ou dans un en-tête HTTP `x-functions-key`. Pour les fonctions non-WebHook, vous pouvez indiquer qu’aucune clé API n’est requise en définissant la propriété `authLevel` sur "anonymous" dans le fichier *function.json*.

Les valeurs de clé API figurent dans le dossier *D:\\home\\data\\Functions\\secrets* du système de fichiers du conteneur de fonctions. La clé principale et la clé de fonction sont définies dans le fichier *host.json*, comme illustré dans cet exemple.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

La clé de fonction de *host.json* est utilisable pour déclencher n’importe quelle fonction, à l’exception de celles qui sont désactivées. En revanche, la clé principale permet de déclencher tous les types de fonctions, même les fonctions désactivées. Vous pouvez configurer une fonction pour qu’elle exige la clé principale en définissant la propriété `authLevel` sur "admin".

Si le dossier *secrets* contient un fichier JSON portant le même nom qu’une fonction, la propriété `key` définie dans ce fichier est également utilisable pour déclencher la fonction, et cette clé ne fonctionnera qu’avec la fonction à laquelle elle se réfère. Par exemple, la clé API relative à une fonction nommée `HttpTrigger` est spécifiée dans le fichier *HttpTrigger.json* du dossier *secrets*. Voici un exemple :

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] Lorsque vous configurez un déclencheur WebHook, ne partagez pas la clé principale avec le fournisseur de WebHooks. Utilisez une clé qui ne fonctionnera qu’avec la fonction traitant le WebHook. La clé principale permet de déclencher tous les types de fonctions, y compris les fonctions désactivées.

### Exemple de code C# pour une fonction de déclencheur HTTP 

Cet exemple de code recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Verbose($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### Exemple de code Node.js pour une fonction de déclencheur HTTP 

Cet exemple de code recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### Exemple de code C# pour une fonction WebHook GitHub 

Cet exemple de code enregistre les commentaires relatifs aux problèmes GitHub.

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Verbose($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### Exemple de code Node.js pour une fonction WebHook GitHub 

Cet exemple de code enregistre les commentaires relatifs aux problèmes GitHub.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## Déclencheur de minuteur

Le fichier *function.json* fournit une expression schedule et un commutateur qui indique si la fonction doit ou non se déclencher immédiatement.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Le déclencheur de minuteur gère automatiquement l’augmentation de la taille des instances multi-instance : une seule instance d’une fonction de minuteur spécifique s’exécutera dans l’ensemble des instances.

### Format de l’expression schedule

L’expression schedule peut correspondre à une [expression CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) comportant 6 champs : {seconde} {minute} {heure} {jour} {mois} {jour de la semaine}. De nombreux documents d’expression cron disponibles en ligne omettent le champ {seconde} ; par conséquent, si vous copiez une expression de l’un de ces documents, vous devrez adapter votre code pour y inclure ce champ supplémentaire.

L’expression schedule peut également présenter le format *hh:mm:ss* afin de spécifier le délai entre chaque déclenchement de la fonction.

Voici quelques exemples d’expressions schedule.

Pour déclencher la fonction toutes les 5 minutes :

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Pour déclencher la fonction immédiatement, puis toutes les deux heures par la suite :

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

Pour déclencher la fonction toutes les 15 secondes :

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

### Exemple de code C# de déclencheur de minuteur

Cet exemple de code C# écrit un journal spécifique chaque fois que la fonction est déclenchée.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Déclencheurs et liaisons (files d’attente, objets blob, tables) Azure Storage

Cette section comporte les sous-sections suivantes :

* [Propriété de connexion Azure Storage dans function.json](#storageconnection)
* [Déclencheur de file d’attente Azure Storage](#storagequeuetrigger)
* [Liaison de sortie de file d’attente Azure Storage](#storagequeueoutput)
* [Déclencheur d’objet blob Azure Storage](#storageblobtrigger)
* [Liaisons d’entrée et de sortie d’objet blob Azure Storage](#storageblobbindings)
* [Liaisons d’entrée et de sortie de tables Azure Storage](#storagetablesbindings)

### <a id="storageconnection"></a> Propriété de connexion Azure Storage dans function.json

Pour tous les déclencheurs et liaisons Azure Storage, le fichier *function.json* inclut une propriété `connection`. Par exemple :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "queueTrigger",
            "direction": "in",
            "queueName": "myqueue-items",
            "connection":""
        }
    ]
}
```

Si vous laissez la propriété `connection` vide, le déclencheur ou la liaison fonctionneront avec le compte de stockage par défaut défini pour le conteneur de fonctions. Si vous souhaitez que le déclencheur ou la liaison fonctionnent avec un autre compte de stockage, créez dans le conteneur de fonctions un paramètre d’application qui pointe vers le compte de stockage à utiliser, puis définissez `connection` sur le nom du paramètre d’application. Pour ajouter un paramètre d’application, procédez comme suit :

1. Dans le panneau **Conteneur de fonctions** du portail Azure, cliquez sur **Paramètres de conteneur de fonctions > Accéder aux paramètres App Service**.

2. Dans le panneau **Paramètres**, cliquez sur **Paramètres de l’application**.

3. Faites défiler les éléments vers le bas jusqu’à la section **Paramètres de l’application**, puis ajoutez une entrée avec **Clé** = *{valeur unique de votre choix}* et **Valeur** = chaîne de connexion du compte de stockage.

### <a id="storagequeuetrigger"></a> Déclencheur de file d’attente Azure Storage

Le fichier *function.json* fournit le nom de la file d’attente à interroger et le nom de variable pour le message de file d’attente. Par exemple :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "queueTrigger",
            "direction": "in",
            "queueName": "myqueue-items",
            "connection":""
        }
    ]
}
```

#### Types pris en charge pour le déclencheur de file d’attente

Le message de file d’attente peut être désérialisé vers l’un des types suivants :

* `string`
* `byte[]`
* Objet JSON   
* `CloudQueueMessage`

#### Métadonnées de déclencheur de file d’attente

Vous pouvez obtenir les métadonnées de file d’attente dans votre fonction en utilisant les noms de variable suivants :

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger (autre méthode pour récupérer le texte du message de file d’attente sous la forme d’une chaîne)

Cet exemple de code C# récupère et enregistre les métadonnées de file d’attente :

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### Gestion des messages de file d’attente incohérents

Les messages dont le contenu provoque l'échec d'une fonction sont appelés *messages incohérents*. Lorsque la fonction échoue, le message en file d’attente n’est pas supprimé, mais finalement récupéré à nouveau, provoquant la répétition du cycle. Le Kit de développement logiciel (SDK) peut interrompre automatiquement le cycle après un nombre limité d’itérations, ou vous pouvez le faire manuellement.

Le Kit de développement logiciel (SDK) appelle une fonction jusqu’à cinq fois pour traiter un message de file d’attente. Si la cinquième tentative échoue, le message est déplacé vers une file d’attente de messages incohérents.

La file d'attente de messages incohérents se nomme *{nom\_file\_d'attente\_d'origine}*-poison. Vous pouvez écrire une fonction pour traiter les messages de la file d’attente de messages incohérents en les consignant dans un journal ou en envoyant une notification signalant qu’une attention manuelle est nécessaire.

Si vous souhaitez gérer les messages incohérents manuellement, vous pouvez obtenir le nombre de fois où un message a été récupéré pour traitement en consultant `dequeueCount`.

### <a id="storagequeueoutput"></a> Liaison de sortie de file d’attente Azure Storage

Le fichier *function.json* fournit le nom de la file d’attente de sortie et un nom de variable pour le contenu du message. Cet exemple utilise un déclencheur de file d’attente et écrit un message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "type": "queue",
      "queueName": "samples-workitems-out",
      "connection": "",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Types pris en charge pour la liaison de sortie de file d’attente

La liaison `queue` peut sérialiser les types ci-après vers un message de file d’attente :

* `string` (crée le message en file d’attente si la valeur du paramètre n’est pas null lorsque la fonction se termine)
* `byte[]` (fonctionne comme le type string) 
* `CloudQueueMessage` (fonctionne comme le type string) 
* Objet JSON (crée un message avec un objet Null si le paramètre présente la valeur Null lorsque la fonction se termine)

#### Exemple de code de liaison de sortie de file d’attente

Cet exemple de code C# écrit un message de file d’attente de sortie spécifique pour chaque message de file d’attente d’entrée.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

Cet exemple de code C# écrit plusieurs messages en utilisant `ICollector<T>` (spécifiez `IAsyncCollector<T>` dans une fonction asynchrone) :

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

### <a id="storageblobtrigger"></a> Déclencheur d’objet blob Azure Storage

Le fichier *function.json* fournit un chemin d’accès qui spécifie le conteneur à surveiller, et éventuellement un modèle de nom d’objet blob. Cet exemple code un déclencheur pour les objets blob qui sont ajoutés au conteneur samples-workitems.

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

> [AZURE.NOTE] Si le conteneur d’objets blob que le déclencheur surveille contient plus de 10 000 objets blob, le runtime Functions analyse les fichiers journaux pour effectuer le suivi des objets blob nouveaux ou modifiés. Ce processus ne se déroule pas en temps réel ; il se peut qu’une fonction ne se déclenche que quelques minutes ou plus après la création de l’objet blob. En outre, des [journaux de stockage sont créés dans la mesure du possible](https://msdn.microsoft.com/library/azure/hh343262.aspx) ; il n’est pas garanti que tous les événements soient capturés. Dans certaines conditions, des journaux peuvent être omis. Si les limitations relatives à la vitesse et à la fiabilité des déclencheurs d’objet blob pour les conteneurs volumineux ne sont pas acceptables pour votre application, il est recommandé de créer un message de file d’attente lorsque vous créez l’objet blob, et d’utiliser un déclencheur de file d’attente plutôt qu’un déclencheur d’objet blob pour traiter cet objet blob.

#### Types pris en charge pour le déclencheur d’objet blob

Les objets blob peuvent être désérialisés vers les types suivants :

* string
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

#### Exemple de code C# de déclencheur d’objet blob

Cet exemple de code C# enregistre le contenu de chaque objet blob qui est ajouté au conteneur.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
}
```

#### Modèles de nom de déclencheur d’objet blob

Vous pouvez spécifier un modèle de nom d’objet blob dans la propriété `path`. Par exemple :

```json
"path": "input/original-{name}",
```

Ce chemin d’accès trouverait un objet blob nommé *original-Blob1.txt* dans le conteneur *input*, et la variable `name` du code de fonction présenterait la valeur `Blob1`.

Autre exemple :

```json
"path": "input/{blobname}.{blobextension}",
```

Ce chemin d’accès trouverait également un objet blob nommé *original-Blob1.txt*, et les variables `blobname` et `blobextension` du code de fonction présenteraient respectivement les valeurs *original-Blob1* et *txt*.

Vous pouvez restreindre les types d’objets blob qui déclenchent la fonction en spécifiant un modèle avec une valeur fixe pour l’extension de fichier. Si vous définissez la propriété `path` sur *samples/{nom}.png*, seuls les objets blob *.png* du conteneur *samples* déclencheront la fonction.

Si vous devez spécifier un modèle de nom pour les noms d’objet blob qui présentent des accolades, doublez ces dernières. Par exemple, si vous souhaitez rechercher des objets blob, dans le conteneur *images*, qui présentent des noms comme suit :

		{20140101}-soundfile.mp3

utilisez la valeur ci-après pour la propriété `path` :

		images/{{20140101}}-{name}

Dans cet exemple, la variable `name` présenterait la valeur *soundfile.mp3*.

#### Reçus d’objets blob

Le runtime Azure Functions vérifie qu’aucune fonction de déclencheur d’objet blob n’est appelée plusieurs fois pour un même objet blob, nouveau ou mis à jour. Pour ce faire, il tient à jour les *reçus d’objets blob* afin de déterminer si la version d’un objet blob donné a été traitée.

Les reçus d’objets blob sont stockés dans un conteneur appelé *azure-webjobs-hosts* associé au compte de stockage Microsoft Azure indiqué par la chaîne de connexion AzureWebJobsStorage. Un reçu d’objet blob contient les informations suivantes :

* Fonction appelée pour l’objet blob ("*{nom du conteneur de fonctions}*.Functions.*{nom de la fonction}*", par exemple : "functionsf74b96f7.Functions.CopyBlob")
* Nom du conteneur
* Type d’objet blob (« BlockBlob » ou « PageBlob »)
* Nom de l’objet blob
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

Si vous souhaitez forcer le retraitement d’un objet blob, vous pouvez supprimer manuellement le reçu de l’objet blob à partir du conteneur *azure-webjobs-hosts*.

#### Gestion des objets blob incohérents

Lorsqu’une fonction de déclencheur d’objet blob échoue, le Kit de développement logiciel (SDK) l’appelle de nouveau, au cas où l’échec aurait été provoqué par une erreur temporaire. Si le problème est occasionné par le contenu de l’objet blob, la fonction échoue chaque fois qu’elle tente de traiter cet objet. Par défaut, le Kit de développement logiciel (SDK) appelle une fonction jusqu’à 5 fois pour un objet blob donné. En cas d’échec après la cinquième tentative, le Kit de développement logiciel (SDK) ajoute un message à la file d’attente nommée *webjobs-blobtrigger-poison*.

Le message en file d’attente associé aux objets blob incohérents correspond à un objet JSON, qui contient les propriétés suivantes :

* FunctionId (au format *{nom du conteneur de fonctions}*.Functions.*{nom de la fonction}*)
* BlobType (« BlockBlob » ou « PageBlob »)
* ContainerName
* BlobName
* ETag (identificateur de version de l’objet blob, par exemple : « 0x8D1DC6E70A277EF »)

### <a id="storageblobbindings"></a> Liaisons d’entrée et de sortie d’objet blob Azure Storage

Le fichier *function.json* fournit les noms de conteneur et de variable pour le nom et le contenu d’objet blob. Cet exemple utilise un déclencheur de file d’attente pour copier un objet blob :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Types pris en charge pour l’entrée et la sortie d’objet BLOB

La liaison `blob` peut sérialiser ou désérialiser les types suivants :

* `Stream`
* `TextReader`
* `TextWriter`
* `string` (pour l’objet blob de sortie : crée un objet blob uniquement si le paramètre de chaîne ne présente pas la valeur Null lorsque la fonction renvoie les résultats)
* Objet JSON (pour l’objet blob de sortie : crée un objet blob en tant qu’objet Null si le paramètre présente la valeur Null lorsque la fonction se termine)
* `CloudBlobStream` (sortie uniquement)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### Exemple de code C# de sortie d’objet blob

Cet exemple de code C# copie un objet blob dont le nom est reçu dans un message de file d’attente.

```CSHARP
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a id="storagetablesbindings"></a> Liaisons d’entrée et de sortie de tables Azure Storage

Le fichier *function.json* relatif aux tables de stockage fournit plusieurs propriétés :

* `name` : nom de la variable à utiliser dans le code pour la liaison de table.
* `tableName`
* `partitionKey` et `rowKey` : propriétés utilisées conjointement pour lire une entité unique dans une fonction C# ou Node, ou pour écrire une entité unique dans une fonction Node.
* `take` : nombre maximal de lignes à lire pour l’entrée de table dans une fonction Node.
* `filter` : expression de filtre OData pour l’entrée de table dans une fonction Node.

Ces propriétés prennent en charge les scénarios suivants :

* Lecture d’une seule ligne dans une fonction C# ou Node.

	Définissez `partitionKey` et `rowKey`. Les propriétés `filter` et `take` ne sont pas utilisées dans ce scénario.

* Lecture de plusieurs lignes dans une fonction C#.

	Le runtime Functions fournit un objet `IQueryable<T>` lié à la table. Le type `T` doit dériver de `TableEntity` ou implémenter `ITableEntity`. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario ; s’il y a lieu, vous pouvez utiliser l’objet `IQueryable` pour effectuer le filtrage éventuellement requis.

* Lecture de plusieurs lignes dans une fonction Node.

	Définissez les propriétés `filter` et `take`. Ne définissez pas `partitionKey` ni `rowKey`.

* Écriture d’une ou de plusieurs lignes dans une fonction C#.

	Le runtime Functions fournit un objet `ICollector<T>` ou `IAsyncCollector<T>` lié à la table, où `T` spécifie le schéma des entités à ajouter. En général, le type `T` dérive de `TableEntity` ou implémente `ITableEntity`, mais ce n’est pas obligatoire. Les propriétés `partitionKey`, `rowKey`, `filter` et `take` ne sont pas utilisées dans ce scénario.

#### Lire une entité de table unique en C# ou en Node

Cet exemple *function.json* utilise un déclencheur de file d’attente pour lire une ligne de table unique, avec une valeur de clé de partition codée en dur et la clé de ligne fournie dans le message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
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
      "connection": "",
      "direction": "in"
    }
  ],
  "disabled": false
}
```
L’exemple de code C# ci-après fonctionne avec le fichier *function.json* précédent pour lire une entité de table unique. Le message de file d’attente comporte la valeur de clé de ligne, et l’entité de table est lue dans un type défini dans le fichier *run.csx*. Le type inclut les propriétés `PartitionKey` et `RowKey`, et ne dérive pas de `TableEntity`.

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
    log.Verbose($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

L’exemple de code Node ci-après fonctionne également avec le fichier *function.json* précédent pour lire une entité de table unique.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### Lire plusieurs entités de table en C## 

L’exemple de fichier *function.json* et de code C# ci-après lit les entités relatives à une clé de partition spécifiée dans le message de file d’attente.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
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
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Verbose($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### Créer des entités de table en C## 

Les exemples de fichiers *function.json* et *run.csx* ci-après indiquent comment écrire des entités de table en C#.

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
      "connection": "",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Verbose($"Adding Person entity {i}");
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

#### Créer une entité de table en Node

Les exemples de fichiers *function.json* et *run.csx* ci-après indiquent comment écrire une entité de table en Node.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Liaisons Azure DocumentDB

Cette section comporte les sous-sections suivantes :

* [Liaison d’entrée Azure DocumentDB](#docdbinput)
* [Liaison de sortie Azure DocumentDB](#docdboutput)

### <a id="docdbinput"></a> Liaison d’entrée Azure DocumentDB

Les liaisons d’entrée peuvent charger un document à partir d’une collection DocumentDB et le transmettre directement à votre liaison. L’ID du document peut être déterminé en fonction du déclencheur qui a appelé la fonction. Dans une fonction C#, toutes les modifications apportées à l’enregistrement sont automatiquement renvoyées à la collection une fois que la fonction s’est correctement terminée.

Le fichier function.json fournit les propriétés ci-après à utiliser avec la liaison d’entrée DocumentDB :

- `name` : nom de variable utilisé dans le code de fonction pour le document.
- `type` : propriété devant être définie sur "documentdb".
- `databaseName` : base de données contenant le document.
- `collectionName` : collection contenant le document.
- `id` : ID du document à récupérer. Cette propriété prend en charge les liaisons semblables à "{queueTrigger}", qui utiliseront la valeur de chaîne du message de file d’attente en tant qu’ID du document.
- `connection` : cette chaîne doit correspondre à un paramètre d’application défini sur le point de terminaison de votre compte DocumentDB. Si vous choisissez votre compte dans l’onglet Intégrer, un nouveau paramètre d’application est créé à votre intention avec un nom indiqué sous la forme VotreCompte\_DOCUMENTDB. Si vous devez créer le paramètre d’application manuellement, la chaîne de connexion réelle doit se présenter sous la forme suivante : AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;.
- direction : propriété devant être définie sur *"in"*.

Exemple de fichier function.json :
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Exemple de code d’entrée Azure DocumentDB pour un déclencheur de file d’attente C#
 
En fonction de l’exemple de fichier function.json ci-dessus, la liaison d’entrée DocumentDB récupérera le document dont l’ID correspond à la chaîne de message de file d’attente et le transmettra au paramètre "document". Si ce document est introuvable, le paramètre "document" présentera la valeur Null. Le document sera ensuite mis à jour avec la nouvelle valeur de texte une fois la fonction terminée.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Exemple de code d’entrée Azure DocumentDB pour un déclencheur de file d’attente Node.js
 
En fonction de l’exemple de fichier function.json ci-dessus, la liaison d’entrée DocumentDB récupérera le document dont l’ID correspond à la chaîne de message de file d’attente et le transmettra à la propriété de liaison `documentIn`. Dans les fonctions Node.js, les documents mis à jour ne sont pas renvoyés à la collection. Toutefois, vous pouvez transmettre directement la liaison d’entrée à une liaison de sortie DocumentDB nommée `documentOut` pour prendre en charge les mises à jour. Cet exemple de code met à jour la propriété de texte du document d’entrée et définit cet élément comme document de sortie.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

### <a id="docdboutput"></a> Liaisons de sortie Azure DocumentDB

Vos fonctions peuvent écrire des documents JSON dans une base de données Azure DocumentDB à l’aide de la liaison de sortie **Document Azure DocumentDB**. Pour plus d’informations sur Azure DocumentDB, voir l’article [Présentation de DocumentDB](../documentdb/documentdb-introduction.md) et le [didacticiel de mise en route](../documentdb/documentdb-get-started.md).

Le fichier function.json fournit les propriétés ci-après à utiliser avec la liaison de sortie DocumentDB :

- `name` : nom de variable utilisé dans le code de fonction pour le nouveau document.
- `type` : propriété devant être définie sur *"documentdb"*.
- `databaseName` : base de données contenant la collection dans laquelle le nouveau document sera créé.
- `collectionName` : collection dans laquelle le nouveau document sera créé.
- `createIfNotExists` : valeur booléenne indiquant si la collection sera ou non créée si elle n’existe pas. La valeur par défaut est *false*. Cette configuration est due au fait que les collections sont créées avec un débit réservé, ce qui a des conséquences sur le plan tarifaire. Pour plus d’informations, voir la [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection` : cette chaîne doit correspondre à un **Paramètre d’application** défini sur le point de terminaison de votre compte DocumentDB. Si vous choisissez votre compte dans l’onglet **Intégrer**, un nouveau paramètre d’application est créé à votre intention avec un nom indiqué sous la forme `yourAccount_DOCUMENTDB`. Si vous devez créer le paramètre d’application manuellement, la chaîne de connexion réelle doit se présenter sous la forme `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction` : doit être définie sur *"out"*. 
 
Exemple de fichier function.json :

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Exemple de code de sortie Azure DocumentDB pour un déclencheur de file d’attente Node.js

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

Document de sortie :

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### Exemple de code de sortie Azure DocumentDB pour un déclencheur de file d’attente C#


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Exemple de code de sortie Azure DocumentDB définissant le nom du fichier

Si vous souhaitez spécifier le nom du document dans la fonction, définissez simplement la valeur `id`. Par exemple, si le contenu JSON d’un employé, tel que celui ci-dessous, a été supprimé dans la file d’attente :

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

Vous pourriez utiliser le code C# suivant dans une fonction de déclencheur de file d’attente :
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

Exemple de sortie :

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Liaisons de tables faciles Azure Mobile Apps

Azure App Service Mobile Apps vous permet d’exposer les données de point de terminaison de table aux clients mobiles. Ces mêmes données tabulaires sont utilisables dans les liaisons d’entrée et de sortie avec Azure Functions. Lorsque vous disposez d’une application mobile principale Node.js, vous pouvez travailler avec ces données tabulaires dans le portail Azure en utilisant des *tables faciles*. Les tables faciles prennent en charge le schéma dynamique, de sorte que des colonnes sont automatiquement ajoutées pour correspondre à la forme des données en cours d’insertion, ce qui simplifie le développement du schéma. Le schéma dynamique est activé par défaut et doit être désactivé dans une application mobile de production. Pour plus d’informations sur les tables faciles dans Mobile Apps, voir [Procédure : utilisation de l’outil Easy Tables dans le portail Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing). Notez que pour l’instant, les tables faciles du portail ne sont pas prises en charge pour les applications mobiles principales .NET. Vous pouvez toujours utiliser les liaisons de fonction des points de terminaison de table d’application mobile principale .NET ; toutefois, le schéma dynamique n’est pas pris en charge pour les applications mobiles principales .NET.

Cette section comporte les sous-sections suivantes :

* [Clé API de tables faciles Azure Mobile Apps](#easytablesapikey)
* [Liaison d’entrée de tables faciles Azure Mobile Apps](#easytablesinput)
* [Liaison de sortie de tables faciles Azure Mobile Apps](#easytablesoutput)

### <a id="easytablesapikey"></a> Utiliser une clé API pour sécuriser l’accès à vos points de terminaison de tables faciles Mobile Apps

Pour l’instant, Azure Functions ne peut pas accéder aux points de terminaison sécurisés par l’authentification App Service. Cela signifie que tous les points de terminaison Mobile Apps utilisés dans vos fonctions avec des liaisons de tables faciles doivent autoriser l’accès anonyme, lequel constitue la valeur par défaut. Les liaisons de tables faciles vous permettent de spécifier une clé API, qui est une clé secrète partagée pouvant être utilisée pour empêcher les accès indésirables à partir d’applications autres que vos fonctions. Mobile Apps n’intègre aucune prise en charge de l’authentification par clé API. Toutefois, vous pouvez implémenter une clé API dans votre application mobile principale Node.js en suivant les exemples présentés dans [Azure App Service Mobile Apps backend implementing an API key](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key) (Serveur principal Azure App Service Mobile Apps implémentant une clé API).

>[AZURE.IMPORTANT] Cette clé API ne doit pas être distribuée avec vos clients d’application mobile ; elle doit uniquement être distribuée de façon sécurisée aux clients côté service, comme Azure Functions.

### <a id="easytablesinput"></a> Liaison d’entrée de tables faciles Azure Mobile Apps

Les liaisons d’entrée peuvent charger un enregistrement à partir d’un point de terminaison de table Mobile Apps et le transmettre directement à votre liaison. L’ID d’enregistrement est déterminé en fonction du déclencheur qui a appelé la fonction. Dans une fonction C#, toutes les modifications apportées à l’enregistrement sont automatiquement renvoyées à la table une fois que la fonction s’est correctement terminée.

Le fichier function.json prend en charge l’utilisation des propriétés ci-après avec les liaisons d’entrée de tables faciles Mobile Apps :

- `name` : nom de variable utilisé dans le code de fonction pour le nouvel enregistrement.
- `type` : type de liaison devant être défini sur *easyTable*.
- `tableName` : table dans laquelle sera créé le nouvel enregistrement.
- `id` : ID de l’enregistrement à récupérer. Cette propriété prend en charge les liaisons semblables à `{queueTrigger}`, qui utiliseront la valeur de chaîne du message de file d’attente en tant qu’ID de l’enregistrement.
- `apiKey` : chaîne correspondant au paramètre d’application qui spécifie la clé API facultative pour l’application mobile. Cette propriété est requise lorsque votre application mobile utilise une clé API pour restreindre l’accès client.
- `connection` : chaîne correspondant au paramètre d’application qui spécifie l’URI de votre application mobile.
- `direction` : sens de la liaison, qui doit être défini sur *in*.

Exemple de fichier function.json :

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "easyTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### Exemple de code de tables faciles Azure Mobile Apps pour un déclencheur de file d’attente C#

En fonction de l’exemple de fichier function.json ci-dessus, la liaison d’entrée récupère l’enregistrement dont l’ID correspond à la chaîne de message de file d’attente et le transmet au paramètre *record*. Si l’enregistrement est introuvable, le paramètre présente la valeur Null. L’enregistrement est ensuite mis à jour avec la nouvelle valeur *Text* une fois la fonction terminée.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Exemple de code de tables faciles Azure Mobile Apps pour un déclencheur de file d’attente Node.js

En fonction de l’exemple de fichier function.json ci-dessus, la liaison d’entrée récupère l’enregistrement dont l’ID correspond à la chaîne de message de file d’attente et le transmet au paramètre *record*. Dans les fonctions Node.js, les enregistrements mis à jour ne sont pas renvoyés à la table. Cet exemple de code écrit l’enregistrement récupéré dans le journal.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


### <a id="easytablesoutput"></a> Liaison de sortie de tables faciles Azure Mobile Apps

Votre fonction peut écrire un enregistrement dans un point de terminaison de table Mobile Apps à l’aide d’une liaison de sortie de tables faciles.

Le fichier function.json prend en charge l’utilisation des propriétés ci-après avec la liaison de sortie de tables faciles :

- `name` : nom de variable utilisé dans le code de fonction pour le nouvel enregistrement.
- `type` : type de liaison devant être défini sur *easyTable*.
- `tableName` : table dans laquelle est créé le nouvel enregistrement.
- `apiKey` : chaîne correspondant au paramètre d’application qui spécifie la clé API facultative pour l’application mobile. Cette propriété est requise lorsque votre application mobile utilise une clé API pour restreindre l’accès client.
- `connection` : chaîne correspondant au paramètre d’application qui spécifie l’URI de votre application mobile.
- `direction` : sens de la liaison, qui doit être défini sur *out*.

Exemple de fichier function.json :

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "easyTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### Exemple de code de tables faciles Azure Mobile Apps pour un déclencheur de file d’attente C#

Cet exemple de code C# insère un nouvel enregistrement avec une propriété *Text* dans la table spécifiée dans la liaison ci-dessus.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Exemple de code de tables faciles Azure Mobile Apps pour un déclencheur de file d’attente Node.js

Cet exemple de code Node.js insère un nouvel enregistrement avec une propriété *text* dans la table spécifiée dans la liaison ci-dessus.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Liaison de sortie de concentrateur de notification Azure

Vos fonctions peuvent envoyer des notifications Push à l’aide d’un concentrateur de notification Azure configuré avec très peu de lignes de code. Toutefois, le concentrateur de notification doit être configuré pour l’infrastructure Platform Notification System (PNS) que vous souhaitez utiliser. Pour plus d’informations sur la configuration d’un concentrateur de notification Azure et sur le développement d’applications clientes qui s’inscrivent aux notifications, voir [Prise en main de Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started.md) en cliquant sur votre plateforme cliente cible au début de l’article.

Le fichier function.json fournit les propriétés ci-après à utiliser avec une liaison de sortie de concentrateur de notification :

- `name` : nom de variable utilisé dans le code de fonction pour le message de concentrateur de notification.
- `type` : propriété devant être définie sur *"notificationHub"*.
- `tagExpression` : expressions de balise vous permettant de demander que les notifications soient remises à un ensemble d’appareils qui se sont inscrits pour la réception de notifications correspondant à l’expression de balise. Pour plus d’informations, voir [Routage et expressions de balise](../notification-hubs/notification-hubs-routing-tag-expressions.md).
- `hubName` : nom de la ressource de concentrateur de notification dans le portail Azure.
- `connection` : cette chaîne de connexion doit correspondre à une chaîne de connexion **Paramètre d’application** définie sur la valeur *DefaultFullSharedAccessSignature* de votre concentrateur de notification.
- `direction` : doit être définie sur *"out"*. 
 
Exemple de fichier function.json :

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

### Configuration de la chaîne de connexion du concentrateur de notification Azure

Pour utiliser une liaison de sortie de concentrateur de notification, vous devez configurer la chaîne de connexion pour le concentrateur. Vous pouvez effectuer cette opération dans l’onglet *Intégrer* : il vous suffit de sélectionner votre concentrateur de notification ou d’en créer un.

Vous pouvez également ajouter manuellement une chaîne de connexion pour un concentrateur existant en ajoutant cette chaîne pour l’élément *DefaultFullSharedAccessSignature* à votre concentrateur de notification. Cette chaîne de connexion fournit l’autorisation d’accès de votre fonction pour l’envoi des messages de notification. La valeur de la chaîne de connexion *DefaultFullSharedAccessSignature* est accessible à partir du bouton **clés** dans le panneau principal de votre ressource de concentrateur de notification dans le portail Azure. Pour ajouter manuellement une chaîne de connexion pour votre concentrateur, procédez comme suit :

1. Dans le panneau **Conteneur de fonctions** du portail Azure, cliquez sur **Paramètres de conteneur de fonctions > Accéder aux paramètres App Service**.

2. Dans le panneau **Paramètres**, cliquez sur **Paramètres de l’application**.

3. Faites défiler les éléments vers le bas jusqu’à la section **Chaînes de connexion** et ajoutez une entrée nommée pour la valeur *DefaultFullSharedAccessSignature* pour votre concentrateur de notification. Redéfinissez le type sur **Personnalisé**.
4. Référencez votre nom de chaîne de connexion dans les liaisons de sortie. Cette chaîne sera semblable à **MyHubConnectionString** utilisé dans l’exemple ci-dessus.

### Exemple de code de concentrateur de notification Azure pour un déclencheur de minuteur Node.js 

Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates.md) contenant `location` et `message`.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

### Exemple de code de concentrateur de notification Azure pour un déclencheur de file d’attente C#

Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates.md) contenant `message`.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

Cet exemple envoie une notification pour une [inscription de modèles](../notification-hubs/notification-hubs-templates.md) qui contient `message` en utilisant une chaîne JSON valide.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

### Exemple de code C# de déclencheur de file d’attente de concentrateur de notification Azure utilisant le type Notification

Cet exemple indique comment utiliser le type `Notification` qui est défini dans la [bibliothèque Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Pour utiliser ce type et la bibliothèque, vous devez charger un fichier *project.json* pour votre conteneur de fonctions. Le fichier project.json est un fichier texte JSON qui ressemblera à celui-ci :

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

Pour plus d’informations sur le chargement de votre fichier project.json, voir [la procédure de chargement d’un fichier project.json](http://stackoverflow.com/questions/36411536/how-can-i-use-nuget-packages-in-my-azure-functions).

Exemple de code :

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Informations de référence pour les développeurs C# sur Azure Functions](functions-reference-csharp.md)
* [Informations de référence pour les développeurs NodeJS sur Azure Functions](functions-reference-node.md)

<!---HONumber=AcomDC_0420_2016-->