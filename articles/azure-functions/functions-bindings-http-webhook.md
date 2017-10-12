---
title: "Liaisons HTTP et webhook d’Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs et des liaisons HTTP et webhook dans Azure Functions."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: mahender
ms.openlocfilehash: 3c3247592cbe2bc382d220264b0c646ee566b8a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Liaisons HTTP et webhook Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et utiliser des déclencheurs et liaisons HTTP dans Azure Functions.
Ces éléments vous permettent d’utiliser Azure Functions pour générer des API sans serveur et répondre aux webhooks.

Fonctions Azure fournit les liaisons suivantes :
- Un [déclencheur HTTP](#httptrigger) vous permet d’appeler une fonction avec une requête HTTP. Vous pouvez personnaliser cela pour répondre aux [webhooks](#hooktrigger).
- Un [liaison de sortie HTTP](#output) permet de répondre à la demande.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

<a name="httptrigger"></a>

## <a name="http-trigger"></a>Déclencheur HTTP
Le déclencheur HTTP exécute votre fonction en réponse à une requête HTTP. Vous pouvez personnaliser la fonction pour répondre à une URL particulière ou à un ensemble de méthodes HTTP. Vous pouvez également configurer un déclencheur HTTP pour répondre aux webhooks. 

Si vous utilisez le portail des fonctions, vous pouvez également commencer immédiatement à utiliser un modèle existant. Sélectionnez **Nouvelle fonction**, puis choisissez « API et webhooks » dans la liste déroulante **Scénario**. Sélectionnez un des modèles, puis cliquez sur **Créer**.

Par défaut, un déclencheur HTTP répond à la requête avec un code d’état HTTP 200 OK et un corps vide. Pour modifier la réponse, configurez une [liaison de sortie HTTP](#output).

### <a name="configuring-an-http-trigger"></a>Configuration d’un déclencheur HTTP
Un déclencheur HTTP est défini par un objet JSON du tableau `bindings` de function.json, comme dans l’exemple suivant :

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "get" ],
    "route": "values/{id}"
},
```
La liaison prend en charge les propriétés suivantes :

|Propriété  |Description  |
|---------|---------|
| **name** | Obligatoire : nom de variable utilisé dans le code de la fonction pour la requête ou le corps de la requête. Voir [Utilisation d’un déclencheur HTTP à partir d’un code](#httptriggerusage). |
| **type** | Obligatoire : doit être défini sur `httpTrigger`. |
| **direction** | Obligatoire : doit être défini sur `in`. |
| **authLevel** | Détermine, le cas échéant, les clés qui doivent être présentes dans la requête pour appeler la fonction. Les valeurs possibles sont les suivantes : <ul><li><code>anonymous</code>&mdash;Aucune clé API n’est obligatoire.</li><li><code>function</code>&mdash;Une clé API spécifique à la fonction est obligatoire. Il s’agit de la valeur par défaut si aucune valeur n’est fournie.</li><li><code>admin</code>&mdash;La clé principale est obligatoire.</li></ul> Pour plus d’informations, consultez [Utilisation de clés](#keys). |
| **methods** | Tableau des méthodes HTTP auxquelles la fonction répond. À défaut de spécification, la fonction répond à toutes les méthodes HTTP. Voir [Personnalisation du point de terminaison HTTP](#url). |
| **route** | Définit le modèle de routage, en contrôlant les URL de requête auxquelles votre fonction répond. La valeur par défaut est `<functionname>`. Pour plus d’informations, consultez [Personnalisation du point de terminaison HTTP](#url). |
| **webHookType** | Configure le déclencheur HTTP pour qu’il agisse en tant que récepteur de webhook pour le fournisseur spécifié. N’utilisez pas la propriété _methods_ lors de l’utilisation de ce paramètre. Les valeurs possibles sont les suivantes :<ul><li><code>genericJson</code>&mdash;Point de terminaison webhook à usage général sans logique pour un fournisseur spécifique.</li><li><code>github</code>&mdash;La fonction répond à des webhooks GitHub. N’utilisez pas la propriété _authLevel_ lors de l’utilisation de cette valeur.</li><li><code>slack</code>&mdash;La fonction répond à des webhooks Slack. N’utilisez pas la propriété _authLevel_ lors de l’utilisation de cette valeur.</li></ul> Pour plus d’informations, consultez [Réponse aux webhooks](#hooktrigger). |

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Utilisation d’un déclencheur HTTP à partir d’un code
Pour les fonctions C# et F#, vous pouvez déclarer le type de votre entrée de déclencheur comme un `HttpRequestMessage` ou un type .NET personnalisé. Si vous choisissez `HttpRequestMessage`, vous obtenez un accès complet à l’objet de la requête. Pour un type .NET personnalisé, le runtime Functions tente d’analyser le corps de la requête JSON pour définir les propriétés de l’objet. 

Dans le cas des fonctions Node.js, le runtime Functions fournit le corps de requête plutôt que l’objet de requête. Pour plus d’informations, consultez [Exemples de déclencheur HTTP](#httptriggersample).


<a name="output"></a>
## <a name="http-response-output-binding"></a>Liaison de sortie de réponse HTTP
Utilisez la liaison de sortie HTTP pour répondre à l’expéditeur de la demande HTTP. Cette liaison requiert un déclencheur HTTP, et vous permet de personnaliser la réponse associée à la demande du déclencheur. Si aucune liaison de sortie HTTP n’est spécifiée, un déclencheur HTTP retourne HTTP 200 OK avec un corps vide. 

### <a name="configuring-an-http-output-binding"></a>Configuration d’une liaison de sortie HTTP
Une liaison de sortie HTTP est définie par un objet JSON du tableau `bindings` de function.json, comme dans l’exemple suivant :

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
La liaison prend en charge les propriétés obligatoires suivantes :

|Propriété  |Description  |
|---------|---------|
|**name** | Nom de variable utilisé dans le code de fonction pour la réponse. Voir [Utilisation d’une liaison de sortie HTTP à partir d’un code](#outputusage). |
| **type** |Doit être défini sur `http`. |
| **direction** | Doit être défini sur `out`. |

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Utilisation d’une liaison de sortie HTTP à partir d’un code
Vous pouvez utiliser le paramètre de sortie pour répondre à l’appelant HTTP ou webhook. Vous pouvez également utiliser les modèles de réponse de la norme du langage. Pour voir des exemples de réponses, consultez [Exemples de déclencheur HTTP](#httptriggersample) et [Exemples de déclencheur Webhook](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Réponse aux webhooks
Un déclencheur HTTP avec la propriété _webHookType_ est configuré pour répondre aux [webhooks](https://en.wikipedia.org/wiki/Webhook). La configuration de base utilise le paramètre « genericJson ». Cela limite les demandes à celles utilisant HTTP Post et le type de contenu `application/json`.

Le déclencheur peut en outre être adapté à un fournisseur de webhook spécifique, tel que [GitHub](https://developer.github.com/webhooks/) ou [Slack](https://api.slack.com/outgoing-webhooks). Si un fournisseur est spécifié, le runtime Functions peut gérer la logique de validation du fournisseur à votre place.  

### <a name="configuring-github-as-a-webhook-provider"></a>Configurer GitHub en tant que fournisseur de webhooks
Pour répondre aux webhooks GitHub, commencez par créer votre fonction avec un déclencheur HTTP, puis définissez la propriété **webHookType** sur `github`. Ensuite, copiez son [URL](#url) et sa [clé API](#keys) dans la page **Ajouter un Webhook** de votre dépôt GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Pour voir un exemple, consultez [Créer une fonction déclenchée par un webhook GitHub](functions-create-github-webhook-triggered-function.md).

### <a name="configuring-slack-as-a-webhook-provider"></a>Configuration de Slack en tant que fournisseur de webhook
La webhook de Slack génère un jeton à votre place au lieu de vous laisser le spécifier. Vous devez donc configurer une clé spécifique de la fonction avec le jeton reçu de Slack. Voir [Utilisation de clés](#keys).

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>Personnalisation du point de terminaison HTTP
Par défaut, lorsque vous créez une fonction pour un déclencheur HTTP ou un WebHook, la fonction est adressable avec un itinéraire de la forme :

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Vous pouvez personnaliser cet itinéraire en utilisant la propriété facultative `route` sur la liaison d’entrée du déclencheur HTTP. Par exemple, le fichier *function.json* suivant définit une propriété `route` pour un déclencheur HTTP :

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Avec cette configuration, la fonction est désormais adressable avec l’itinéraire suivant au lieu de l’itinéraire d’origine.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

Cela permet au code de la fonction de prendre en charge deux paramètres dans l’adresse, _category_ et _id_. Vous pouvez utiliser les [contraintes d’itinéraire des API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) de votre choix avec vos paramètres. Le code de la fonction C# suivant utilise les deux paramètres.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Voici le code de la fonction Node.js qui utilise les mêmes paramètres d’itinéraire.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
} 
```

Par défaut, tous les itinéraires de fonction sont préfixés par *api*. Vous pouvez également personnaliser ou supprimer le préfixe avec la propriété `http.routePrefix` dans votre fichier *host.json*. L’exemple suivant supprime le préfixe d’itinéraire *api* en sélectionnant une chaîne vide pour le préfixe dans le fichier *host.json*.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

Pour plus d’informations sur la mise à jour du fichier *host.json* de votre fonction, consultez [Mettre à jour les fichiers Function App](functions-reference.md#fileupdate). 

Pour plus d’informations sur les autres propriétés que vous pouvez configurer dans votre fichier *host.json*, consultez la [référence host.json](functions-host-json.md).


<a name="keys"></a>
## <a name="working-with-keys"></a>Utilisation de clés
Les déclencheurs HTTP vous permettent d’utiliser des clés pour une sécurité accrue. Un déclencheur HTTP standard peut les utiliser comme une clé API, en exigeant que la clé soit présente dans la requête. Les webhooks peuvent utiliser des clés pour autoriser des demandes de plusieurs façons, selon ce que le fournisseur prend en charge.

Les clés sont stockées dans votre Function App dans Azure, et chiffrées au repos. Pour afficher vos clés, créez des clés, ou restaurez des clés avec de nouvelles valeurs, accédez à l’une de vos fonctions au sein du portail, puis sélectionnez « Gérer ». 

Il existe deux types de clés :
- **Clés d’hôte** : ces clés sont partagées par toutes les fonctions au sein de la Function App. Utilisées en tant que clés API, elles permettent d’accéder à toute fonction au sein de la Function App.
- **Clés de fonction** : ces clés s’appliquent uniquement aux fonctions spécifiques sous lesquelles elles sont définies. Utilisées en tant que clés API, elles permettent d’accéder uniquement à ces fonctions.

Chaque clé est nommée pour référence et il existe une clé par défaut (nommée « default ») au niveau fonction et hôte. La **clé principale** est une clé d’hôte par défaut nommée « _master » qui est définie pour chaque application de fonction. Cette clé ne peut pas être révoquée. Elle fournit un accès administratif aux API de runtime. L’utilisation de `"authLevel": "admin"` dans le JSON de liaison nécessite que cette clé soit présentée à la requête. Une autre clé entraînerait un échec de l’autorisation.

> [!IMPORTANT]  
> En raison des autorisations élevées qu’accorde la clé principale, vous ne devez pas partager celle-ci avec des tiers, ou la distribuer dans des applications clientes natives. Faites preuve de prudence lorsque vous choisissez le niveau d’autorisation administrateur.

### <a name="api-key-authorization"></a>Autorisation de clé API
Par défaut, un déclencheur HTTP nécessite qu’une clé API se trouve dans la requête HTTP. Votre requête HTTP doit donc ressembler à ceci :

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Cette clé peut être incluse dans une variable de chaîne de requête nommée `code` ou dans un en-tête HTTP `x-functions-key`. La valeur de la clé peut être toute clé de fonction définie pour la fonction, ou toute clé d’hôte.

Vous pouvez autoriser les requêtes anonymes, qui ne nécessitent pas de clés. Vous pouvez également exiger que la clé principale soit utilisée. Pour modifier le niveau d’autorisation par défaut, utilisez la propriété `authLevel` dans le JSON de liaison. Pour plus d’informations, consultez [Déclencheur HTTP](#httptrigger).

### <a name="keys-and-webhooks"></a>Clés et webhooks
Une autorisation de webhook est gérée par le composant récepteur de webhook, qui fait partie du déclencheur HTTP, et le mécanisme varie en fonction du type de webhook. Toutefois, chaque mécanisme dépend d’une clé. Par défaut, la clé de fonction nommée « default » est utilisée. Pour utiliser une autre clé, configurez le fournisseur de webhook pour envoyer le nom de clé avec la requête de l’une des manières suivantes :

- **Chaîne de requête** : le fournisseur passe le nom de clé dans le paramètre de chaîne de requête `clientid` (par exemple, `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`).
- **En-tête de demande** : le fournisseur transmet le nom de clé dans l’en-tête `x-functions-clientid`.

> [!NOTE]
> Les clés de fonction prennent le pas sur les clés d’hôte. Quand deux clés portent le même nom, la clé de fonction est toujours utilisée.


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>Exemples de déclencheur HTTP
Supposons que le tableau `bindings` de function.json contient le déclencheur HTTP suivant :

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

Voir l’exemple spécifique du langage qui recherche un paramètre `name` dans la chaîne de requête ou dans le corps de la requête HTTP.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.JS](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>Exemple de déclencheur HTTP en C# #
```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

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

Vous pouvez également établir une liaison avec un objet .NET au lieu de **HttpRequestMessage**. Cet objet est créé à partir du corps de la requête et analysé en tant que JSON. De même, un type peut être passé à la liaison de sortie de réponse HTTP, et être retourné en tant que corps de la réponse, avec un code d’état 200.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
}
```

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>Exemple de déclencheur HTTP en F# #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Vous avez besoin d’un fichier `project.json` qui utilise NuGet pour référencer les assemblys `FSharp.Interop.Dynamic` et `Dynamitey` de la façon suivante :

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

Ce code utilise NuGet pour récupérer (fetch) vos dépendances, puis les référence dans votre script.

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Exemple de déclencheur HTTP en Node.js
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
     
<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Exemples de webhook
Supposons que le tableau `bindings` de function.json contient le déclencheur de webhook suivant :

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

Voir l’exemple spécifique du langage qui journalise les commentaires de problème GitHub.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.JS](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>Exemple de Webhook en C# #
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

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>Exemple de Webhook en F# #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Exemple de webhook en Node.js
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

