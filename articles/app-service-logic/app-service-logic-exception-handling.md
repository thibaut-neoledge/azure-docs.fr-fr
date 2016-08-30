<properties
   pageTitle="Gestion des exceptions Logic Apps | Microsoft Azure"
   description="Découvrez les modèles de gestion des erreurs et des exceptions avec Azure Logic Apps"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/10/2016"
   ms.author="jehollan"/>

# Gestion des erreurs et des exceptions Logic Apps

Logic Apps propose un ensemble complet d’outils et de modèles afin de garantir la résistance et la fiabilité de vos intégrations contre les défaillances. L’un des principaux défis liés à l’architecture d’intégration est de s’assurer que les temps d’arrêt ou les problèmes des systèmes dépendants sont gérés correctement. Logic Apps optimise la gestion des erreurs, en vous offrant les outils dont vous avez besoin pour gérer les exceptions et les erreurs qui se produisent dans vos flux de travail.

## Stratégies de nouvelle tentative

Le type de gestion des erreurs et des exceptions le plus simple consiste à utiliser une stratégie de nouvelle tentative. Cette stratégie définit si l’action doit faire l’objet d’une nouvelle tentative en cas d’expiration ou d’échec de la demande initiale (toute demande ayant entraîné une réponse 429 ou 5xx). Par défaut, toutes les actions font l’objet de 3 tentatives supplémentaires sur des intervalles de 20 secondes. Par conséquent, si la première demande reçoit une réponse `500 Internal Server Error`, le moteur de flux de travail s’interrompt pendant 20 secondes, puis tente à nouveau d’exécuter la demande. Si à l’issue de toutes les tentatives, la réponse renvoie toujours une exception ou un échec, le flux de travail continue et marque l’état de l’action comme `Failed`.

Vous pouvez configurer des stratégies de nouvelle tentative dans les **entrées** d’une action en particulier. Une stratégie de nouvelle tentative peut être configurée pour effectuer jusqu’à 4 tentatives avec des intervalles d’1 heure. Des détails complémentaires sur les propriétés d’entrée sont [disponibles sur MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Si vous voulez que votre action HTTP effectue 4 tentatives et attende 10 minutes entre chacune d’entre elles, vous devez utiliser la définition suivante :

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Pour plus d’informations sur la syntaxe prise en charge, consultez la [section relative aux stratégies de nouvelle tentative sur MSDN][retryPolicyMSDN].

## Propriété RunAfter pour identifier les échecs

Chaque action d’application logique déclare les actions qui sont nécessaires à son démarrage. Vous pouvez considérer cela comme l’ordre des étapes dans votre flux de travail. Ce classement est connu sous le nom de propriété `runAfter` dans la définition de l’action. Il s’agit d’un objet qui décrit les actions et les états d’action susceptibles d’exécuter l’action. Par défaut, toutes les actions ajoutées par le biais du concepteur sont définies avec la propriété `runAfter` par rapport à l’étape précédente, si celle-ci était `Succeeded`. Toutefois, vous pouvez personnaliser cette valeur pour déclencher des actions lorsque les actions précédentes sont `Failed`, `Skipped` ou un ensemble de valeurs possible. Si vous souhaitez ajouter un élément à une rubrique Service Bus désignée suite à l’échec d’une action spécifique `Insert_Row`, vous pouvez utiliser la configuration `runAfter` suivante :

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ "content-type" : "application/json" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Notez que la propriété `runAfter` est définie pour se déclencher si l’action `Insert_Row` est `Failed`. Pour exécuter l’action si l’état de l’action est `Succeeded`, `Failed` ou `Skipped`, la syntaxe est la suivante :

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] Les actions qui s’exécutent suite à l’échec d’une action précédente et qui sont correctement exécutées, seront marquées comme `Succeeded`. Ce comportement signifie que si vous avez correctement intercepté tous les échecs dans un flux de travail, l’exécution elle-même est marquée comme `Succeeded`.

## Étendues et résultats permettant d’évaluer les actions

De la même manière que vous pouvez exécuter des actions individuelles, vous pouvez également regrouper des actions au sein d’une [étendue](app-service-logic-loops-and-scopes.md) qui agit comme un regroupement logique d’actions. Les étendues sont utiles pour organiser vos actions d’application logique et pour effectuer des évaluations d’agrégation sur l’état d’une étendue. L’étendue elle-même reçoit un état une fois toutes les actions qu’elle comporte terminées. L’état de l’étendue est déterminé selon les mêmes critères qu’une exécution. Si la dernière action d’une branche de l’exécution est `Failed` ou `Aborted`, l’état est `Failed`.

Vous pouvez utiliser la propriété `runAfter` sur une étendue qui a été marquée comme `Failed` pour déclencher des actions spécifiques en raison d’échecs survenus dans l’étendue. L’exécution suite à l’échec d’une étendue vous permet de créer une seule action pour intercepter des échecs, si *des* actions figurent dans l’étendue.

### Obtention du contexte des échecs avec les résultats

L’interception des échecs d’une étendue est très utile, mais vous pouvez également avoir besoin du contexte pour identifier précisément les actions qui ont échoué, ainsi que les codes d’erreur ou d’état renvoyés. La fonction de flux de travail `@result()` fournit le contexte dans le résultat de toutes les actions au sein d’une étendue.

`@result()` prend un paramètre unique, le nom de l’étendue, et renvoie un tableau de tous les résultats d’action dans cette étendue. Ces objets d’action incluent les mêmes attributs que l’objet `@actions()`, y compris l’heure de début de l’action, l’heure de fin de l’action, l’état de l’action, les entrées de l’action, les ID de corrélation d’action, ainsi que ses résultats. Vous pouvez facilement associer une fonction `@result()` avec une propriété `runAfter` pour envoyer le contexte de toutes les actions qui ont échoué dans une étendue.

Si vous souhaitez exécuter une action *for each* dans une étendue marquée comme `Failed`, vous pouvez associer `@result()` avec une action **[Filtrer le tableau](../connectors/connectors-native-query.md)** et une boucle **[ForEach](app-service-logic-loops-and-scopes.md)**. Cela vous permet de filtrer le tableau de résultats sur les actions qui ont échoué. Vous pouvez prendre le tableau des résultats filtrés et effectuer une action pour chaque échec à l’aide de la boucle **ForEach**. Voici un exemple ci-dessous, suivi d’une explication détaillée. Cet exemple envoie une demande HTTP POST avec le corps de réponse de toutes les actions qui ont échoué dans l’étendue `My_Scope`.

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Voici une description détaillée de ce qui se passe :

1. L’action **Filtrer le tableau** permettant de filtrer `@result('My_Scope')` pour obtenir le résultat de toutes les actions au sein de `My_Scope`
1. La condition de l’action **Filtrer le tableau** est tout élément `@result()` dont l’état est égal à `Failed`. Le tableau de tous les résultats d’action de `My_Scope` seront filtrés selon un tableau des résultats d’action ayant échoué.
1. Exécution d’une action **For Each** sur les résultats du **tableau filtré**. Cette opération effectue une action *pour chaque* résultat d’action ayant entraîné un échec filtré ci-dessus.
    - Si une seule action a échoué dans l’étendue, les actions dans `foreach` ne sont exécutées qu’une seule fois. De nombreuses actions ayant échoué peuvent provoquer une action par échec.
1. Envoi d’une requête HTTP POST sur le corps de réponse de l’élément `foreach`, ou `@item()['outputs']['body']`. La forme de l’élément `@result()` est identique à la forme `@actions()` et peut être analysée de la même façon.
1. Deux en-têtes personnalisés avec le nom de l’action qui a échoué `@item()['name']` sont également inclus ainsi que l’ID de suivi du client d’exécution qui a échoué `@item()['clientTrackingId']`.

Pour référence, voici un exemple d’un élément `@result()` unique. Vous pouvez voir les propriétés `name`, `body` et `clientTrackingId` analysées dans l’exemple ci-dessus. Il convient de noter qu’en dehors de `foreach`, `@result()` retourne un tableau de ces objets.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/foo/bar does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Vous pouvez utiliser les expressions ci-dessus pour exécuter différents modèles de gestion des exceptions. Vous pouvez choisir d’exécuter une seule action de gestion en dehors de l’étendue qui accepte l’intégralité du tableau filtré d’échecs et de supprimer `foreach`. Vous pouvez également inclure d’autres propriétés utiles à partir de la réponse `@result()` ci-dessus.

## Azure Diagnostics et télémétrie

Les modèles ci-dessus sont très utiles pour gérer les erreurs et les exceptions d’une exécution, mais vous pouvez également identifier les erreurs et y répondre indépendamment de l’exécution elle-même. [Azure Diagnostics](app-service-logic-monitor-your-logic-apps.md) fournit un moyen simple d’envoyer tous les événements de flux de travail (y compris tous les états d’exécution et d’action) à un compte Azure Storage ou un concentrateur d’événements Azure. Vous pouvez surveiller les journaux et les mesures ou les publier dans n’importe quel outil de surveillance de votre choix pour évaluer les états d’exécution. Vous avez également la possibilité de transmettre tous les événements via le concentrateur d’événements Azure dans [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). Dans Stream Analytics, vous pouvez écrire des requêtes actives sans aucune anomalie, des moyennes ou des échecs dans les journaux de diagnostic. Stream Analytics peut facilement exporter ses résultats vers d’autres sources de données telles que les files d’attente, les rubriques, SQL, DocumentDB et Power BI.

## Étapes suivantes
- [Découvrez comme un client a développé une gestion des erreurs robuste grâce à Logic Apps](app-service-logic-scenario-error-and-exception-handling.md)
- [Consultez d’autres exemples et scénarios Logic Apps](app-service-logic-examples-and-scenarios.md)
- [Découvrez comment créer des déploiements automatisés d’applications logiques](app-service-logic-create-deploy-template.md)
- [Créer et déployer des applications logiques dans Visual Studio](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9

<!---HONumber=AcomDC_0817_2016-->