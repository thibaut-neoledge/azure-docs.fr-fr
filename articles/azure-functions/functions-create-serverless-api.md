---
title: "Créer une API sans serveur à l’aide d’Azure Functions | Microsoft Docs"
description: "Guide pratique : créer une API sans serveur à l’aide d’Azure Functions"
services: functions
author: mattchenderson
manager: erikre
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/04/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2b11d08b95d658ae2b0303107367549e6d0b5dff
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-serverless-api-using-azure-functions"></a>Créer une API sans serveur à l’aide d’Azure Functions

Dans ce didacticiel, vous découvrirez comment Azure Functions vous permet de générer des API très évolutives. Azure Functions est fourni avec une collection de liaisons et de déclencheurs HTTP intégrés qui permettent de créer un point de terminaison dans différents langages, notamment Node.js ou C#. Dans ce didacticiel, vous allez personnaliser un déclencheur HTTP pour gérer des actions spécifiques dans votre conception d’API. Vous allez également préparer le développement de votre API, l’intégration avec Azure Functions Proxies et la configuration d’API factices. Tout cela s’effectue sur l’environnement de calcul sans serveur de Functions. Vous n’avez donc pas à vous soucier de la mise à l’échelle des ressources et vous pouvez vous concentrer uniquement sur votre logique d’API.

## <a name="prerequisites"></a>Composants requis 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

La fonction résultante sera utilisée pour le reste de ce didacticiel.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez le portail Azure. Pour ce faire, connectez-vous au portail : [https://portal.azure.com](https://portal.azure.com) avec votre compte Azure.

## <a name="customize-your-http-function"></a>Personnaliser une fonction HTTP

Par défaut, votre fonction déclenchée par HTTP est configurée pour accepter n’importe quelle méthode HTTP. Il existe également une URL par défaut de la forme `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Si vous avez suivi le démarrage rapide, `<funcname>` ressemble probablement à « HttpTriggerJS1 ». Dans cette section, vous allez modifier la fonction de façon à répondre uniquement aux demandes GET auprès de l’itinéraire `/api/hello`. 

Accédez à votre fonction sur le Portail Azure. Sélectionnez **Intégrer** dans la barre de navigation gauche.

![Personnalisation d’une fonction HTTP](./media/functions-create-serverless-api/customizing-http.png)

Utilisez les paramètres de déclencheur HTTP spécifiés dans le tableau.

| Champ | Exemple de valeur | Description |
|---|---|---|
| Méthodes HTTP autorisées | Méthodes sélectionnées | Détermine quelles méthodes HTTP peuvent être utilisées pour appeler cette fonction. |
| Méthodes HTTP sélectionnées | GET | Autorise uniquement l’utilisation des méthodes HTTP sélectionnés pour appeler cette fonction. |
| Modèle d’itinéraire | /hello | Détermine l’itinéraire utilisé pour appeler cette fonction. |

Remarque : vous n’avez pas inclus le préfixe du chemin d’accès de base `/api` dans le modèle d’itinéraire, car il est géré par un paramètre global.

Cliquez sur **Save**.

Pour plus d’informations sur la personnalisation des fonctions HTTP, consultez la page [Liaisons HTTP et de webhooks d’Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint).

### <a name="test-your-api"></a>Tester l’API

Ensuite, testez votre fonction pour observer son fonctionnement avec la nouvelle surface d’API.

Revenez à la page de développement en cliquant sur le nom de la fonction dans la barre de navigation gauche.

Cliquez sur **Récupérer l’URL de la fonction** et copiez l’URL. Normalement, elle utilise maintenant l’itinéraire `/api/hello`.

Copiez l’URL dans un nouvel onglet du navigateur ou dans le client REST de votre choix. Les navigateurs utilisent GET par défaut.

Exécutez la fonction et vérifiez qu’elle fonctionne. Vous devrez peut-être fournir le paramètre « name » comme chaîne de requête par conformité au code de démarrage rapide.

Vous pouvez également essayer d’appeler le point de terminaison avec une autre méthode HTTP pour vérifier que la fonction n’est pas exécutée. Pour cela, vous devrez utiliser un client REST, par exemple cURL, Postman ou Fiddler.

## <a name="proxies-overview"></a>Vue d’ensemble des proxys

Dans la section suivante, vous ferez apparaître votre API par le biais d’un proxy. Azure Functions Proxies est une fonctionnalité en préversion qui vous permet de transférer les demandes vers d’autres ressources. Vous définissez un point de terminaison HTTP comme avec le déclencheur HTTP, mais, au lieu d’écrire du code à exécuter lorsque ce point de terminaison est appelé, vous spécifiez l’URL d’une implémentation à distance. Cela vous permet de composer plusieurs sources d’API en une seule surface d’API facile à utiliser pour les clients. C’est particulièrement utile si vous souhaitez créer votre API sous forme de microservices.

Un proxy peut pointer vers n’importe quelle ressource HTTP, notamment :
- Azure Functions 
- Applications API dans [Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- Conteneurs Docker dans [App Service sur Linux](https://docs.microsoft.com/azure/app-service/app-service-linux-readme)
- Toute autre API hébergée

Pour en savoir plus sur les proxys, consultez la page [Travailler avec Azure Functions Proxies (préversion)].

## <a name="create-your-first-proxy"></a>Créer un premier proxy

Dans cette section, vous allez créer un proxy qui sert de serveur frontal à votre API globale. 

### <a name="setting-up-the-frontend-environment"></a>Configuration de l’environnement frontal

Répétez les étapes de la page [Créer une Function App](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) pour créer une Function App dans laquelle vous allez créer votre proxy. Cette nouvelle application servira de serveur frontal à notre API ; la Function App que vous avez modifiée précédemment servira de serveur principal.

Accédez à votre nouvelle Function App frontale sur le portail.

Sélectionnez **Paramètres**. Basculez **Activer Azure Functions Proxies (préversion)** sur « Activé ».

Sélectionnez **Paramètres de la plateforme** et choisissez **Paramètres de l’application**.

Faites défiler jusqu’à **Paramètres de l’application** et créez un nouveau paramètre avec la clé « HELLO_HOST ». Donnez-lui comme valeur l’hôte de votre Function App principale, par exemple `<YourApp>.azurewebsites.net`. Il s’agit d’une partie de l’URL que vous avez copiée au moment de tester votre fonction HTTP. Vous ferez référence à ce paramètre plus tard dans la configuration.

> [!NOTE] 
> Les paramètres de l’application sont recommandés pour la configuration d’hôte afin d’éviter une dépendance à l’environnement codé en dur pour le proxy. Grâce à eux, vous pouvez déplacer la configuration du proxy d’un environnement à l’autre, et les paramètres de l’application propres à l’environnement s’appliqueront.

Cliquez sur **Save**.

### <a name="creating-a-proxy-on-the-frontend"></a>Créer un proxy sur le serveur frontal

Revenez à votre Function App frontale sur le portail.

Dans la barre de navigation gauche, cliquez sur le signe « + » à côté de « Proxies (préversion) ».

![Création d’un proxy](./media/functions-create-serverless-api/creating-proxy.png)

Utilisez les paramètres de proxy spécifiés dans le tableau.

| Champ | Exemple de valeur | Description |
|---|---|---|
| Nom | HelloProxy | Nom convivial utilisé uniquement à des fins de gestion. |
| Modèle d’itinéraire | /api/hello | Détermine l’itinéraire utilisé pour appeler ce proxy. |
| URL principale | https://%HELLO_HOST%/api/hello | Spécifie le point de terminaison vers lequel la demande doit être redirigée via proxy. |

Remarque : les proxys ne fournissent pas de préfixe du chemin d’accès de base `/api` ; il doit être inclus dans le modèle d’itinéraire.

La syntaxe `%HELLO_HOST%` fera référence au paramètre d’application que vous avez créé précédemment. L’URL résolue pointera vers votre fonction d’origine.

Cliquez sur **Create**.

Vous pouvez essayer votre nouveau proxy en copiant l’URL du proxy et en le testant dans le navigateur ou avec le client HTTP de votre choix.

## <a name="create-a-mock-api"></a>Créer une API factice

Maintenant, vous allez utiliser un proxy pour créer une API factice pour votre solution. Cela permet au développement client de progresser, sans que le serveur principal soit nécessairement implémenté dans sa totalité. Dans la suite du développement, vous pourrez créer une nouvelle Function App qui prenne en charge cette logique et y redirige votre proxy.

Pour créer cette API fictive, nous allons créer un nouveau proxy, cette fois en utilisant [l’Éditeur App Service](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Pour commencer, accédez à votre Function App sur le portail. Sélectionnez **Fonctionnalités de la plateforme** et **Éditeur App Service**. L’éditeur App Service s’ouvre dans un nouvel onglet.

Sélectionnez `proxies.json` dans la barre de navigation gauche. Il s’agit du fichier qui stocke la configuration de tous vos proxys. Si vous utilisez l’une des [méthodes de déploiement de Functions](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), c’est le fichier que vous maintiendrez dans le contrôle de code source. Pour en savoir plus sur ce fichier, consultez la page [Configuration avancée des proxys](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Si vous avez suivi toutes les étapes jusqu’à présent, votre proxies.json doit se présenter ainsi :

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Vous allez maintenant ajouter votre API factice. Remplacez votre fichier proxies.json par le code suivant :

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Cela ajoute un nouveau proxy, « GetUserByName », sans la propriété backendUri. Au lieu d’appeler une autre ressource, il modifie la réponse par défaut des proxys par substitution de réponse. Les substitutions de demandes et de réponses peuvent également être utilisées en association avec une URL principale. C’est particulièrement utile pour la redirection via proxy vers un système hérité, où vous devrez peut-être modifier les en-têtes, interroger des paramètres, etc. Pour en savoir plus sur les substitutions de demandes et de réponses, consultez la page [Modifier les demandes et les réponses dans les proxys](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses).

Testez votre API factice en appelant le point de terminaison `/api/users/{username}` à l’aide d’un navigateur ou du client REST de votre choix. Veillez à remplacer _{username}_ par une valeur de chaîne représentant un nom d’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à créer et à personnaliser une API sur Azure Functions. Vous avez également appris à réunir plusieurs API, y compris factices, en une surface d’API unifiée. Vous pouvez utiliser ces techniques pour construire des API plus complexes, qui s’exécutent sur le modèle de calcul sans serveur fourni par Azure Functions.

Les références suivantes peuvent être utiles pour développer davantage votre API :

- [Liaisons HTTP et webhook Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Travailler avec Azure Functions Proxies (préversion)]
- [Documenter une API Azure Functions (préversion)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Travailler avec Azure Functions Proxies (préversion)]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
