---
title: "Liaisons HTTP et webhook d’Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs et des liaisons HTTP et webhook dans Azure Functions."
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2016
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 56d6f7b5858a0e2122021e02718050a26e6defe4
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017


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

Par défaut, un déclencheur HTTP répondra à la demande avec un code d’état HTTP 200 OK et un corps vide. Pour modifier la réponse, configurez une [liaison de sortie HTTP](#output).

### <a name="configuring-an-http-trigger"></a>Configuration d’un déclencheur HTTP
Pour définir un déclencheur HTTP, incluez un objet JSON similaire au suivant dans le tableau `bindings` de function.json :

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

* **name** : obligatoire - nom de variable utilisé dans le code de la fonction pour la demande ou dans le corps de la demande. Voir [Utilisation d’un déclencheur HTTP à partir d’un code](#httptriggerusage).
* **type** : obligatoire - doit être « httpTrigger ».
* **direction** : obligatoire - doit être « in ».
* _authLevel_ : détermine, le cas échéant, les clés qui doivent être présentes dans la demande pour appeler la fonction. Voir [Utilisation de clés](#keys) ci-dessous. Les valeurs possibles sont les suivantes :
    * _anonymous_ : aucune clé API n’est requise.
    * _function_ : une clé API spécifique de la fonction est requise. Il s’agit de la valeur par défaut si aucune valeur n’est fournie.
    * _admin_ : la clé principale est requise.
* **methods** : il s’agit d’un tableau des méthodes HTTP auxquelles la fonction répond. À défaut de spécification, la fonction répond à toutes les méthodes HTTP. Voir [Personnalisation du point de terminaison HTTP](#url).
* **route** : définit le modèle de routage, en contrôlant les URL de demande auxquelles votre fonction répond. La valeur par défaut est `<functionname>`. Voir [Personnalisation du point de terminaison HTTP](#url).
* **webHookType** : configure le déclencheur HTTP pour qu’il agisse en tant que récepteur de webhook pour le fournisseur spécifié. Si cette valeur est choisie, la propriété _methods_ ne doit pas être définie. Voir [Réponse aux webhooks](#hooktrigger). Les valeurs possibles sont les suivantes :
    * _genericJson_ : point de terminaison webhook à usage général sans logique pour un fournisseur spécifique.
    * _github_ : la fonction répond aux webhooks GitHub. Si cette valeur est choisie, la propriété _authLevel_ ne doit pas être définie.
    * _slack_ : la fonction répond aux webhooks Slack. Si cette valeur est choisie, la propriété _authLevel_ ne doit pas être définie.

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>Utilisation d’un déclencheur HTTP à partir d’un code
Pour les fonctions C# et F #, vous pouvez déclarer le type de votre entrée de déclenchement en tant que `HttpRequestMessage` ou type personnalisé. Si vous choisissez `HttpRequestMessage`, vous obtenez un accès complet à l’objet de la demande. Pour un type personnalisé (par exemple, un objet CLR traditionnel), Functions tente d’analyser le corps de la demande en tant que JSON pour compléter les propriétés de l’objet.

Dans le cas des fonctions Node.js, le runtime Functions fournit le corps de requête plutôt que l’objet de requête.

Pour des exemples d’utilisation, voir [Exemples de déclencheur HTTP](#httptriggersample).


<a name="output"></a>
## <a name="http-response-output-binding"></a>Liaison de sortie de réponse HTTP
Utilisez la liaison de sortie HTTP pour répondre à l’expéditeur de la demande HTTP. Cette liaison requiert un déclencheur HTTP, et vous permet de personnaliser la réponse associée à la demande du déclencheur. Si aucune liaison de sortie HTTP n’est spécifiée, un déclencheur HTTP renvoie HTTP 200 OK avec un corps vide. 

### <a name="configuring-an-http-output-binding"></a>Configuration d’une liaison de sortie HTTP
La liaison de sortie HTTP est définie en incluant dans le tableau `bindings` de function.json un objet JSON similaire à celui-ci :

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
La liaison contient les propriétés suivantes :

* **name** : obligatoire - nom de variable utilisé dans le code de fonction pour la réponse. Voir [Utilisation d’une liaison de sortie HTTP à partir d’un code](#outputusage).
* **type** : obligatoire - doit être « http ».
* **direction** : obligatoire - doit être « out ».

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>Utilisation d’une liaison de sortie HTTP à partir d’un code
Vous pouvez utiliser le paramètre de sortie (par exemple, « res ») pour répondre à l’appelant HTTP ou webhook. Vous pouvez également utiliser le modèle `Request.CreateResponse()` (C#) ou `context.res` (Node.JS) standard pour retourner votre réponse. Pour des exemples d’utilisation de cette dernière méthode, voir [Exemples de déclencheurs HTTP](#httptriggersample) et [Exemples de déclencheurs webhook](#hooktriggersample).


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>Réponse aux webhooks
Un déclencheur HTTP avec la propriété _webHookType_ sera configuré pour répondre aux [webhooks](https://en.wikipedia.org/wiki/Webhook). La configuration de base utilise le paramètre « genericJson ». Cela limite les demandes à celles utilisant HTTP Post et le type de contenu `application/json`.

Le déclencheur peut en outre être adapté à un fournisseur de webhook spécifique (par exemple, [GitHub](https://developer.github.com/webhooks/) et [Slack](https://api.slack.com/outgoing-webhooks)). Si un fournisseur est spécifié, le runtime Functions peut se charger de la logique de validation du fournisseur à votre place.  

### <a name="configuring-github-as-a-webhook-provider"></a>Configurer GitHub en tant que fournisseur de webhooks
Pour répondre aux webhooks GitHub, commencez par créer votre fonction avec un déclencheur HTTP, et définir la propriété _webHookType_ sur « github ». Ensuite, copiez son [URL](#url) et sa [clé API](#keys) vers la page **Ajouter un Webhook** de votre référentiel GitHub. Pour plus d’informations, voir la documentation [Création de webhooks](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409).

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

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

Cela permet au code de la fonction de prendre en charge deux paramètres dans l’adresse, « category » et « id ». Vous pouvez utiliser les [contraintes d’itinéraire des API Web](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) de votre choix avec vos paramètres. Le code de la fonction C# suivant utilise les deux paramètres.

```csharp
    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
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

Pour plus d’informations sur les autres propriétés que vous pouvez configurer dans votre fichier *host.json*, consultez la [référence host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).


<a name="keys"></a>
## <a name="working-with-keys"></a>Utilisation de clés
HttpTriggers peut tirer parti des clés pour renforcer la sécurité. Un HttpTrigger standard peut les utiliser en tant que clés API, en exigeant que la clé soit présente dans la requête. Les webhooks peuvent utiliser des clés pour autoriser des demandes de plusieurs façons, selon ce que le fournisseur prend en charge.

Les clés sont stockées dans votre Function App dans Azure, et chiffrées au repos. Pour afficher vos clés, créez des clés, ou restaurez des clés avec de nouvelles valeurs, accédez à l’une de vos fonctions au sein du portail, puis sélectionnez « Gérer ». 

Il existe deux types de clés :
- **Clés d’hôte** : ces clés sont partagées par toutes les fonctions au sein de la Function App. Utilisées en tant que clés API, elles permettent d’accéder à toute fonction au sein de la Function App.
- **Clés de fonction** : ces clés s’appliquent uniquement aux fonctions spécifiques sous lesquelles elles sont définies. Utilisées en tant que clés API, elles permettent d’accéder uniquement à ces fonctions.

Chaque clé est nommée pour référence et il existe une clé par défaut (nommée « default ») au niveau fonction et hôte. Le **clé principale** est une clé d’hôte par défaut nommée « _master » qui est définie pour chaque Function App et qui ne peut pas être révoquée. Elle fournit un accès administratif aux API de runtime. L’utilisation de `"authLevel": "admin"` dans le JSON de liaison nécessite que cette clé soit présentée à la demande. Une autre clé entraînerait un échec d’autorisation.

> [!NOTE]
> En raison des autorisations élevées qu’accorde la clé principale, vous ne devez pas partager celle-ci avec des tiers, ou la distribuer dans des applications clientes natives. Faites preuve de prudence lors du choix du niveau d’autorisation administrateur.
> 
> 

### <a name="api-key-authorization"></a>Autorisation de clé API
Par défaut, un HttpTrigger requiert une clé API dans la requête HTTP. Par conséquent, votre requête HTTP ressemble normalement à ceci :

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Cette clé peut être incluse dans une variable de chaîne de requête nommée `code` ou dans un en-tête HTTP `x-functions-key`. La valeur de la clé peut être toute clé de fonction définie pour la fonction, ou toute clé d’hôte.

Vous pouvez autoriser des demandes sans clé ou spécifier que la clé principale doit être utilisée en modifiant la propriété `authLevel` dans le JSON de liaison (voir [Déclencheur HTTP](#httptrigger)).

### <a name="keys-and-webhooks"></a>Clés et webhooks
Une autorisation de webhook est gérée par le composant récepteur de webhook, qui fait partie du HttpTrigger, et le mécanisme varie en fonction du type de webhook. Toutefois, chaque mécanisme dépend d’une clé. Par défaut, la clé de fonction nommée « default » est utilisée. Si vous souhaitez utiliser une autre clé, vous devez configurer le fournisseur de webhook pour envoyer le nom de clé avec la demande de l’une des manières suivantes :

- **Chaîne de requête** : le fournisseur transmet le nom de clé dans le paramètre de chaîne de requête `clientid` (par exemple, `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`).
- **En-tête de demande** : le fournisseur transmet le nom de clé dans l’en-tête `x-functions-clientid`.

> [!NOTE]
> Les clés de fonction prennent le pas sur les clés d’hôte. Si les deux clés portent le même nom, la clé de fonction est utilisée.
> 
> 


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

Vous avez besoin d’un fichier `project.json` qui utilise NuGet pour référencer les assemblys `FSharp.Interop.Dynamic` et `Dynamitey`, comme suit :

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

Ce code utilisera NuGet pour extraire vos dépendances et les référencera dans votre script.

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


