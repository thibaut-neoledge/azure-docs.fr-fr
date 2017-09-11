---
title: Test des fonctions Azure Functions | Microsoft Docs
description: "Testez vos fonctions Azure à l’aide de Postman, cURL et Node.js."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur, test"
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: aca03ba4137893157fcbe6650336782ab88cd234
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Stratégies permettant de tester votre code dans Azure Functions

Cette rubrique montre les différentes manières de tester les fonctions, parmi lesquelles les approches générales suivantes :

+ Outils basés sur HTTP, comme cURL, Postman et même un navigateur web pour les déclencheurs basés sur le web
+ Explorateur de stockage Azure pour tester les déclencheurs basés sur le stockage Azure
+ Onglet de test dans le portail Azure Functions
+ Fonction déclenchée par un minuteur
+ Application ou infrastructure de test

Toutes ces méthodes de test utilisent une fonction de déclencheur HTTP qui accepte les entrées via un paramètre de chaîne de requête ou le corps de la requête. Vous allez créer cette fonction dans la première section.

## <a name="create-a-function-for-testing"></a>Créer une fonction de test
Dans ce didacticiel, nous allons utiliser principalement une version légèrement modifiée du modèle de fonction JavaScript HttpTrigger disponible lors de la création d’une fonction. Si vous avez besoin d’aide pour créer une fonction, passez en revue ce [didacticiel](functions-create-first-azure-function.md). Sélectionnez le modèle **HttpTrigger- JavaScript** lors de la création de la fonction de test dans le [portail Azure].

Le modèle de fonction par défaut est essentiellement une fonction « hello world » qui renvoie le nom à partir du corps de la requête ou du paramètre de chaîne de requête, `name=<your name>`.  Nous allons mettre à jour le code pour vous permettre également de fournir le nom et l’adresse en tant que contenu JSON dans le corps de la requête. La fonction les renvoie ensuite vers le client une fois disponibles.   

Mettez à jour la fonction avec le code suivant que nous allons utiliser pour le test :

```javascript
module.exports = function (context, req) {
    context.log("HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
    context.log("Request Headers = " + JSON.stringify(req.headers));
    var res;

    if (req.query.name || (req.body && req.body.name)) {
        if (typeof req.query.name != "undefined") {
            context.log("Name was provided as a query string param...");
            res = ProcessNewUserInformation(context, req.query.name);
        }
        else {
            context.log("Processing user info from request body...");
            res = ProcessNewUserInformation(context, req.body.name, req.body.address);
        }
    }
    else {
        res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done(null, res);
};
function ProcessNewUserInformation(context, name, address) {
    context.log("Processing user information...");
    context.log("name = " + name);
    var echoString = "Hello " + name;
    var res;

    if (typeof address != "undefined") {
        echoString += "\n" + "The address you provided is " + address;
        context.log("address = " + address);
    }
    res = {
        // status: 200, /* Defaults to 200 */
        body: echoString
    };
    return res;
}
```

## <a name="test-a-function-with-tools"></a>Test d’une fonction avec des outils
En dehors du portail Azure, il existe différents outils que vous pouvez utiliser pour déclencher vos fonctions de test. Ceux-ci incluent les outils de test HTTP (basés sur l’interface utilisateur et sur la ligne de commande), les outils d’accès au stockage Azure et même un simple navigateur web.

### <a name="test-with-a-browser"></a>Test avec un navigateur
Le navigateur web fournit un moyen simple de déclencher des fonctions via HTTP. Vous pouvez utiliser un navigateur pour les requêtes GET qui ne nécessitent pas une charge utile du corps et utilisent uniquement des paramètres de chaîne de requête.

Pour tester la fonction que nous avons définie ci-dessus, copiez l’**URL de la fonction** à partir du portail. Elle se présente comme suit :

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Ajoutez le paramètre `name` à la chaîne de requête. Utilisez un nom réel pour l’espace réservé `<Enter a name here>`.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Collez l’URL dans votre navigateur. Vous devriez obtenir une réponse similaire à celle-ci.

![Capture d’écran de l’onglet du navigateur Chrome avec la réponse de test](./media/functions-test-a-function/browser-test.png)

Cet exemple présente le navigateur Chrome, qui encapsule la chaîne renvoyée en XML. D’autres navigateurs affichent seulement la valeur de chaîne.

Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Test avec Postman
L’outil recommandé pour tester la plupart de vos fonctions est Postman, qui s’intègre avec le navigateur Chrome. Pour installer Postman, consultez [Get Postman](https://www.getpostman.com/). Postman permet de contrôler beaucoup plus d’attributs d’une requête HTTP.

> [!TIP]
> Utilisez l’outil de test HTTP qui vous convient le mieux. Les solutions alternatives à Postman sont les suivantes :  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Paw](https://luckymarmot.com/paw)  
>
>

Pour tester la fonction avec un corps de requête dans Postman :

1. Démarrez Postman à partir du bouton **Applications** dans le coin supérieur gauche d’une fenêtre de navigateur Chrome.
2. Copiez votre **URL de fonction** et collez-la dans Postman. Elle inclut le paramètre de chaîne de requête de code d’accès.
3. Changez la méthode HTTP en **POST**.
4. Cliquez sur **Body** > **raw** et ajoutez un corps de requête JSON similaire au suivant :

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Cliquez sur **Envoyer**.

L’illustration suivante montre un exemple de test de fonction d’écho simple dans ce didacticiel.

![Capture d’écran de l’interface utilisateur de Postman](./media/functions-test-a-function/postman-test.png)

Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>Test avec cURL à partir de la ligne de commande
Souvent, lorsque vous testez des logiciels, la ligne de commande suffit pour déboguer votre application. Il en est de même pour les fonctions. Notez que cURL est disponible par défaut sur les systèmes Linux. Sous Windows, vous devez d’abord télécharger et installer [l’outil cURL](https://curl.haxx.se/).

Pour tester la fonction que nous avons définie ci-dessus, copiez l’**URL de la fonction** à partir du portail. Elle se présente comme suit :

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Il s’agit de l’URL qui déclenche votre fonction. Nous pouvons tester cela à l’aide de la commande cURL sur la ligne de commande pour exécuter une requête Get (`-G` ou `--get`) sur la fonction :

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Cet exemple particulier exige un paramètre de chaîne de requête qui peut être transmis en tant que données (`-d`) dans la commande cURL :

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Exécutez la commande. La sortie suivante de la fonction s’affiche sur la ligne de commande :

![Capture d’écran de la sortie de l’invite de commande](./media/functions-test-a-function/curl-test.png)

Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Test d’un déclencheur d’objet blob à l’aide de l’Explorateur de stockage
Vous pouvez tester une fonction de déclenchement d’objet blob à l’aide de l’[Explorateur de stockage Azure](http://storageexplorer.com/).

1. Dans le [portail Azure] de votre application de fonction, créez une fonction de déclencheur d’objet blob C#, F# ou JavaScript. Définissez le chemin d’accès à surveiller sur le nom de votre conteneur d’objets blob. Par exemple :

        files
2. Cliquez sur le bouton **+** pour sélectionner ou créer le compte de stockage que vous souhaitez utiliser. Cliquez ensuite sur **Créer**.
3. Créez un fichier texte contenant le texte suivant et enregistrez-le :

        A text file for blob trigger function testing.
4. Exécutez l’[Explorateur de stockage Azure](http://storageexplorer.com/) et connectez-vous au conteneur d’objets blob dans le compte de stockage surveillé.
5. Cliquez sur **Charger** pour charger le fichier texte.

    ![Capture d’écran de l’explorateur de stockage](./media/functions-test-a-function/azure-storage-explorer-test.png)

Le code de la fonction de déclenchement d’objets blob par défaut consigne le traitement de l’objet blob dans les journaux :

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Test d’une fonction au sein de fonctions
Le portail Azure Functions est conçu pour vous permettre de tester HTTP et les fonctions déclenchées par minuteur. Vous pouvez également créer des fonctions pour déclencher d’autres fonctions que vous testez.

### <a name="test-with-the-functions-portal-run-button"></a>Test avec le bouton d’exécution du portail Functions
Le portail fournit un bouton **Exécuter** qui vous permet d’effectuer des tests limités. Vous pouvez fournir un corps de requête à l’aide du bouton mais vous ne pouvez pas fournir de paramètres de chaîne de requête ou mettre à jour des en-têtes de demande.

Testez la fonction de déclenchement HTTP que nous avons créée précédemment en ajoutant une chaîne JSON similaire à la suivante dans le champ **Corps de requête**, puis cliquez sur le bouton **Exécuter**.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Test avec un déclencheur de minuteur
Certaines fonctions ne peuvent pas être correctement testées avec les outils mentionnés précédemment. C’est le cas, par exemple, d’une fonction de déclenchement de file d’attente qui s’exécute lorsqu’un message est déposé dans [Stockage File d’attente Azure](../storage/queues/storage-dotnet-how-to-use-queues.md). Vous pouvez toujours écrire du code pour déposer un message dans votre file d’attente. Vous trouverez ci-après un exemple appliqué à un projet de console. Toutefois, une autre approche vous permet de tester des fonctions directement.  

Vous pouvez utiliser un déclencheur de minuteur configuré avec une liaison de sortie de file d’attente. Ce code de déclenchement du minuteur peut ensuite écrire des messages test dans la file d’attente. Cette section présente un exemple.

Pour des informations plus détaillées sur l’utilisation des liaisons avec les fonctions Azure, consultez la rubrique [Azure Functions developer reference](functions-reference.md)(Référence pour les développeurs Azure Functions).

#### <a name="create-a-queue-trigger-for-testing"></a>Créer un déclencheur de file d’attente pour le test
Pour illustrer cette approche, nous allons d’abord créer une fonction de déclenchement de file d’attente que nous voulons tester pour une file d’attente nommée `queue-newusers`. Cette fonction traite les informations de nom et d’adresse d’un nouvel utilisateur déposées dans Stockage File d'attente.

> [!NOTE]
> Si vous utilisez un nom de file d’attente différent, assurez-vous que le nom que vous utilisez est conforme aux règles d’ [affectation de noms pour les files d’attente et les métadonnées](https://msdn.microsoft.com/library/dd179349.aspx) . Sinon, vous recevez un message d’erreur.
>
>

1. Dans le [portail Azure] de votre application de fonction, cliquez sur **Nouvelle fonction** > **QueueTrigger - C#**.
2. Entrez le nom de file d’attente que la fonction de file d’attente doit surveiller :

        queue-newusers
3. Cliquez sur le bouton **+** pour sélectionner ou créer le compte de stockage que vous souhaitez utiliser. Cliquez ensuite sur **Créer**.
4. Laissez cette fenêtre de navigateur du portail ouverte afin de surveiller les entrées de journal du code de modèle de fonction de file d’attente par défaut.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Créer un déclencheur de minuteur pour déposer un message dans la file d’attente
1. Ouvrez le [portail Azure] dans une nouvelle fenêtre de navigateur et accédez à votre application de fonction.
2. Cliquez sur **Nouvelle fonction** > **TimerTrigger - C#**. Entrez une expression cron pour définir la fréquence à laquelle le code du minuteur exécutera le test de votre fonction de file d’attente. Cliquez ensuite sur **Créer**. Si vous souhaitez que le test s’exécute toutes les 30 secondes, vous pouvez utiliser l’[expression CRON](https://wikipedia.org/wiki/Cron#CRON_expression)suivante :

        */30 * * * * *
3. Cliquez sur l’onglet **Intégrer** pour votre nouveau déclencheur de minuteur.
4. Sous **Sortie**, cliquez sur le bouton **+ Nouvelle sortie**. Puis cliquez sur **File d’attente** et sur **Sélectionner**.
5. Notez le nom que vous utilisez pour l’**objet de message de file d’attente**. Il est utilisé dans le code de fonction du minuteur.

        myQueue
6. Entrez le nom de la file d’attente vers laquelle le message est envoyé :

        queue-newusers
7. Cliquez sur le bouton **+** pour sélectionner le compte de stockage que vous avez utilisé précédemment avec le déclencheur de file d’attente. Cliquez ensuite sur **Enregistrer**.
8. Cliquez sur l’onglet **Développer** de votre déclencheur de minuteur.
9. Vous pouvez utiliser le code suivant pour la fonction de minuteur C# dans la mesure où vous avez utilisé le même nom d’objet de message de file d’attente que celui illustré précédemment. Cliquez ensuite sur **Enregistrer**.

    ```cs
    using System;

    public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
    {
        String newUser =
        "{\"name\":\"User testing from C# timer function\",\"address\":\"XYZ\"}";

        log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
        log.Verbose($"{newUser}");   

        myQueue = newUser;
    }
    ```

À ce stade, la fonction de minuteur C# s’exécute toutes les 30 secondes si vous avez utilisé l’exemple d’expression cron. Les journaux de la fonction de minuteur consignent chaque exécution :

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

Dans la fenêtre du navigateur de la fonction de file d’attente, vous pouvez voir chaque message traité :

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>Test d’une fonction avec un code
Vous pouvez avoir besoin de créer une application ou une infrastructure externe pour tester vos fonctions.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Test d’une fonction de déclenchement HTTP avec un code Node.js
Vous pouvez utiliser une application Node.js pour exécuter une requête HTTP afin de tester votre fonction.
Vous devez définir :

* Le paramètre `host` dans les options de requête sur l’hôte de votre application de fonction.
* Votre nom de fonction dans le paramètre `path`.
* Votre code d’accès (`<your code>`) dans le paramètre `path`.

Exemple de code :

```javascript
var http = require("http");

var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";

var nameBodyJSON = {
    name : "Wes testing with Node.JS code",
    address : "Dallas, T.X. 75201"
};

var bodyString = JSON.stringify(nameBodyJSON);

var options = {
  host: "functions841def78.azurewebsites.net",
  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
  method: "POST",
  headers : {
      "Content-Type":"application/json",
      "Content-Length": Buffer.byteLength(bodyString)
    }    
};

callback = function(response) {
  var str = ""
  response.on("data", function (chunk) {
    str += chunk;
  });

  response.on("end", function () {
    console.log(str);
  });
}

var req = http.request(options, callback);
console.log("*** Sending name and address in body ***");
console.log(bodyString);
req.end(bodyString);
```


Output:

    C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
    *** Sending name and address in body ***
    {"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
    Hello Wes testing with Node.JS code
    The address you provided is Dallas, T.X. 75201

Dans la fenêtre **Journaux** du portail, une sortie similaire à la suivante est journalisée lors de l’exécution de la fonction :

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>Test d’une fonction de déclenchement de file d’attente avec un code : C# #
Nous avons mentionné plus tôt que vous pouvez tester un déclencheur de file d’attente à l’aide d’un code pour déposer un message dans votre file d’attente. L’exemple de code suivant est basé sur le code C# présenté dans le didacticiel [Prise en main du Stockage File d'attente Azure](../storage/queues/storage-dotnet-how-to-use-queues.md). Le code des autres langages est également disponible via ce lien.

Pour tester ce code dans une application de console, vous devez :

* [Configurer votre chaîne de connexion du stockage dans le fichier app.config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Transmettez les paramètres `name` et `address` à l’application. Par exemple, `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Ce code accepte le nom et l’adresse d’un nouvel utilisateur en tant qu’arguments de ligne de commande pendant l’exécution.)

Exemple de code C# :

```cs
static void Main(string[] args)
{
    string name = null;
    string address = null;
    string queueName = "queue-newusers";
    string JSON = null;

    if (args.Length > 0)
    {
        name = args[0];
    }
    if (args.Length > 1)
    {
        address = args[1];
    }

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference(queueName);

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    if (name != null)
    {
        if (address != null)
            JSON = String.Format("{{\"name\":\"{0}\",\"address\":\"{1}\"}}", name, address);
        else
            JSON = String.Format("{{\"name\":\"{0}\"}}", name);
    }

    Console.WriteLine("Adding message to " + queueName + "...");
    Console.WriteLine(JSON);

    CloudQueueMessage message = new CloudQueueMessage(JSON);
    queue.AddMessage(message);
}
```

Dans la fenêtre du navigateur de la fonction de file d’attente, vous pouvez voir chaque message traité :

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[portail Azure]: https://portal.azure.com

