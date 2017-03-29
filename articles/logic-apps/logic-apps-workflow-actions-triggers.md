---
title: "Actions et déclencheurs de workflow - Azure Logic Apps | Microsoft Docs"
description: 
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/17/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 3a240ff317e1b3ea450703965629c08053668856
ms.lasthandoff: 03/22/2017

---

# <a name="workflow-actions-and-triggers-for-azure-logic-apps"></a>Actions et déclencheurs de workflow pour Azure Logic Apps

Les applications logiques se composent de déclencheurs et d’actions. Il existe six types de déclencheurs. Chaque type présente une interface et un comportement différents. Pour plus d’informations, vous pouvez également examiner le [langage de définition de flux de travail](logic-apps-workflow-definition-language.md).  
  
Poursuivez la lecture de cet article pour en savoir plus sur les déclencheurs et les actions, et la manière dont vous pouvez les utiliser pour créer des applications logiques et améliorer vos processus et workflows d’entreprise.  
  
### <a name="triggers"></a>Déclencheurs  

Un déclencheur spécifie les appels qui peuvent initialiser l’exécution de votre workflow d’application logique. Voici deux moyens différents d’initialiser l’exécution de votre workflow :  
  
-   Un déclencheur d’interrogation  

-   Un déclencheur d’émission (en appelant l’[API REST du service de workflow](https://docs.microsoft.com/rest/api/logic/workflows))  
  
Tous les déclencheurs contiennent ces éléments de niveau supérieur :  
  
```json
"<name-of-the-trigger>" : {
    "type": "<type-of-trigger>",
    "inputs": { <settings-for-the-call> },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Week|Month|Year",
        "interval": "<recurrence interval in units of frequency>"
    },
    "conditions": [ <array-of-required-conditions > ],
    "splitOn" : "<property to create runs for>",
    "operationOptions": "<operation options on the trigger>"
}
```

### <a name="trigger-types-and-their-inputs"></a>Types de déclencheurs et entrées associées  

Vous pouvez utiliser les types de déclencheurs suivants :
  
-   **Request** \- Fait de l’application logique un point de terminaison que vous pouvez appeler.  
  
-   **Recurrence** \- Se déclenche selon une planification définie.  
  
-   **HTTP**\- Interroge un point de terminaison web HTTP. Le point de terminaison HTTP doit être conforme à un contrat de déclenchement spécifique, soit en utilisant un modèle asynchrone 202, soit en renvoyant un tableau.  
  
-   **ApiConnection** \- Effectue une interrogation comme le déclencheur HTTP, mais en tirant parti des [API gérées par Microsoft](https://docs.microsoft.com/azure/connectors/apis-list).  
  
-   **HTTPWebhook** \- Ouvre un point de terminaison de la même manière que le déclencheur manuel, mais en appelant également une URL spécifique à des fins d’inscription et de désinscription.  
  
-   **ApiConnectionWebhook**\- Fonctionne de la même manière que le déclencheur HTTPWebhook, mais en tirant parti des API gérées par Microsoft.       
    Chaque type de déclencheur présente un ensemble différent d’**entrées** qui définissent son comportement.  
  
## <a name="request-trigger"></a>Déclencheur de requête  

Ce déclencheur joue un rôle de point de terminaison que vous appelez via une requête HTTP pour appeler votre application logique. Un déclencheur request se présente comme dans cet exemple :  
  
```json
"<name-of-the-trigger>" : {
    "type" : "request",
    "kind": "http",
    "inputs" : {
        "schema" : {
            "properties" : {
                "myInputProperty1" : { "type" : "string" },
                "myInputProperty2" : { "type" : "number" }
            },
        "required" : [ "myInputProperty1" ],
        "type" : "object"
        }
    }
} 
```

Il existe également une propriété facultative appelée **schema** :  
  
|Nom de l'élément|Requis|Description|  
|----------------|------------|---------------|  
|schema|Non|Schéma JSON qui valide la requête entrante. Utile pour aider les étapes de workflow suivantes à déterminer les propriétés auxquelles faire référence.|

Pour appeler ce point de terminaison, vous devez appeler l’API *listCallbackUrl*. Consultez [API REST du service de workflow](https://docs.microsoft.com/rest/api/logic/workflows).  
  
## <a name="recurrence-trigger"></a>Déclencheur recurrence  

Un déclencheur recurrence s’exécute selon une planification définie. Ce type de déclencheur peut se présenter comme dans cet exemple :  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

Comme vous pouvez le constater, il offre un moyen simple d’exécuter un workflow.  
  
|Nom de l'élément|Requis|Description|  
|----------------|------------|---------------|  
|frequency|Oui|Fréquence à laquelle le déclencheur s’exécute. Utilisez une seule de ces valeurs possibles : second (seconde), minute, hour (heure), day (jour), week (semaine), month (mois) ou year (année).|  
|interval|Oui|Intervalle de la fréquence donnée pour la récurrence|  
|startTime|Non|Si une heure de début (startTime) est spécifiée sans décalage UTC, ce fuseau horaire (timeZone) est utilisé.|  
|timeZone|no|Si une heure de début (startTime) est spécifiée sans décalage UTC, ce fuseau horaire (timeZone) est utilisé.|  
  
Vous pouvez également planifier le démarrage de l’exécution d’un déclencheur à un moment ultérieur. Par exemple, si vous souhaitez démarrer un rapport hebdomadaire tous les lundis, vous pouvez planifier le démarrage de l’application logique tous les lundis en créant le déclencheur suivant :  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2015-06-22T00:00:00Z"
    }
}
```

## <a name="http-trigger"></a>Déclencheur HTTP  

Les déclencheurs HTTP interrogent un point de terminaison spécifique et vérifient la réponse pour déterminer si le workflow doit être exécuté. L’objet d’entrée prend l’ensemble des paramètres requis pour construire un appel HTTP :  
  
|Nom de l'élément|Requis|Description|Type|  
|----------------|------------|---------------|--------|  
|statique|yes|Peut être l’une des méthodes HTTP suivantes : GET, POST, PUT, DELETE, PATCH ou HEAD.|String|  
|URI|yes|Point de terminaison HTTP ou HTTPS qui est appelé. La longueur maximale est de 2 Ko.|String|  
|requêtes|Non|Objet représentant les paramètres de requête à ajouter à l’URL. Par exemple, `"queries" : { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL.|Object|  
|headers|Non|Objet représentant chacun des en-têtes envoyés à la requête. Par exemple, pour définir la langue et le type sur une requête :`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|Object|  
|body|Non|Objet représentant la charge utile envoyée au point de terminaison.|Object|  
|retryPolicy|Non|Objet permettant de personnaliser le comportement de nouvelle tentative pour les erreurs 4xx ou 5xx.|Object|  
|authentication|Non|Représente la méthode à utiliser pour authentifier la requête. Pour plus d’informations sur cet objet, consultez [Authentification sortante de Scheduler](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication). En plus de Scheduler, une autre propriété est prise en charge : `authority`. Par défaut, cette propriété est définie sur `https://login.windows.net` lorsqu’aucune valeur n’est spécifiée, mais vous pouvez utiliser une autre audience comme `https://login.windows\-ppe.net`.|Object|  
  
Le déclencheur HTTP nécessite que l’API HTTP se conforme à un modèle spécifique pour fonctionner correctement avec votre application logique. Il requiert les champs suivants :  
  
|Response|Description|  
|------------|---------------|  
|Code d’état|Code d’état 200 \(OK\) pour déclencher une exécution. Les autres codes d’état ne déclenchent pas d’exécution.|  
|En-tête Retry\-After|Nombre de secondes au bout duquel l’application logique interroge à nouveau le point de terminaison.|  
|En-tête Location|URL à appeler lors du prochain intervalle d’interrogation. Si aucune valeur n’est spécifiée, l’URL d’origine est utilisée.|  
  
Voici quelques exemples de comportements différents pour les différents types de requêtes :  
  
|Response code|Retry\-After|Comportement|  
|-----------------|----------------|------------|  
|200|\(Aucune\)|Déclencheur non valide, Retry\-After est requis, sinon le moteur n’interrogera jamais la requête suivante.|  
|202|60|Ne pas déclencher le workflow. La tentative suivante aura lieu une minute après.|  
|200|10|Exécuter le workflow, et vérifier si du contenu supplémentaire est disponible 10 secondes après.|  
|400|\(Aucune\)|Requête incorrecte, ne pas exécuter le workflow. Si aucune **stratégie de nouvelle tentative** n’est définie, la stratégie par défaut est utilisée. Une fois que le nombre de nouvelles tentatives a été atteint, le déclencheur n’est plus valide.|  
|500|\(Aucune\)|Erreur de serveur, ne pas exécuter le workflow.  Si aucune **stratégie de nouvelle tentative** n’est définie, la stratégie par défaut est utilisée. Une fois que le nombre de nouvelles tentatives a été atteint, le déclencheur n’est plus valide.|  
  
Les sorties d’un déclencheur HTTP se présentent comme dans cet exemple :  
  
|Nom de l'élément|Description|Type|  
|----------------|---------------|--------|  
|headers|En-têtes de la réponse HTTP.|Object|  
|body|Corps de la réponse HTTP.|Object|  
  
## <a name="api-connection-trigger"></a>Déclencheur ApiConnection  

Le déclencheur ApiConnection présente des fonctionnalités de base similaires à celles du déclencheur HTTP. Cependant, les paramètres d’identification de l’action sont différents. Voici un exemple :  
  
```json
"dailyReport" : {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            },
        }
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "awesomest"
    }
}
```

|Nom de l'élément|Requis|Type|Description|  
|----------------|------------|--------|---------------|  
|host|Oui||Passerelle et ID d’hôte de l’application d’API.|  
|statique|Oui|String|Peut être l’une des méthodes HTTP suivantes : **GET**, **POST**, **PUT**, **DELETE**, **PATCH** ou **HEAD**.|  
|requêtes|Non|Object|Représente les paramètres de requête à ajouter à l’URL. Par exemple, `"queries" : { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL.|  
|headers|Non|Object|Représente chacun des en-têtes envoyés à la requête. Par exemple, pour définir la langue et le type sur une requête :`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|Non|Object|Représente la charge utile envoyée au point de terminaison.|  
|retryPolicy|Non|Object|Permet de personnaliser le comportement de nouvelle tentative pour les erreurs 4xx ou 5xx.|  
|authentication|Non|Object|Représente la méthode à utiliser pour authentifier la requête. Pour plus d’informations sur cet objet, consultez [Authentification sortante de Scheduler](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication).|  
  
Les propriétés de l’élément host sont les suivantes :  
  
|Nom de l'élément|Requis|Description|  
|----------------|------------|---------------|  
|api runtimeUrl|Oui|Le point de terminaison de l’API gérée.|  
|connection name||Doit être une référence à un paramètre appelé `$connection` et correspond au nom de la connexion d’API gérée que le workflow utilise.|
  
Les sorties du déclencheur ApiConnection sont les suivantes :
  
|Nom de l'élément|Type|Description|  
|----------------|--------|---------------|  
|headers|Object|En-têtes de la réponse HTTP.|  
|body|Object|Corps de la réponse HTTP.|  
  
## <a name="httpwebhook-trigger"></a>Déclencheur HTTPWebhook  

Le déclencheur HTTPWebhook ouvre un point de terminaison de la même manière que le déclencheur manuel, mais il appelle également une URL spécifique à des fins d’inscription et de désinscription. Un déclencheur HTTPWebhook peut se présenter comme dans cet exemple :  

```json
"myappspottrigger": {
    "type": "httpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
    }
```

Un grand nombre de ces sections sont facultatives, et le comportement du webhook varie selon les sections fournies ou omises.  
Les propriétés d’un webhook sont les suivantes :  
  
|Nom de l'élément|Requis|Description|  
|----------------|------------|---------------|  
|subscribe|Non|Demande sortante appelée lorsque le déclencheur est créé et effectue l’inscription initiale.|  
|unsubscribe|Non|Demande sortante lorsque le déclencheur est supprimé.|  
  
-   **subscribe** est l’appel sortant effectué pour commencer à écouter des événements. Cet appel commence par le même ensemble de paramètres que les actions HTTP normales. Cet appel sortant est effectué à chaque fois que le workflow change de quelque façon, par exemple lorsque les informations d’identification sont remplacées ou les paramètres d’entrée du déclencheur modifiés.
  
    En complément de cet appel, il existe une nouvelle fonction : `@listCallbackUrl()`. Cette fonction renvoie une URL unique pour ce déclencheur spécifique dans ce workflow. Elle constitue l’identificateur unique des points de terminaison qui utilisent l’API REST du service.  
  
-   **unsubscribe** est appelé lorsqu’une opération rend ce déclencheur non valide, notamment dans les cas suivants :  
  
    -   Suppression ou désactivation du déclencheur  
  
    -   Suppression ou désactivation du workflow  
  
    -   Suppression ou désactivation de l’inscription  
  
    L’application logique appelle automatiquement l’action unsubscribe. Les paramètres de cette fonction sont les mêmes que ceux du déclencheur HTTP.  
  
    Les sorties du déclencheur HTTPWebhook correspondent au contenu de la demande entrante :  
  
|Nom de l'élément|Type|Description|  
|-----------------|--------|---------------|  
|headers|Object|En-têtes de la requête HTTP.|  
|body|Object|Corps de la requête HTTP.|  

Les limites d’une action webhook peuvent être spécifiées de la même manière que les [limites asynchrones HTTP](#asynchronous-limits).
  

## <a name="conditions"></a>Conditions  

Pour n’importe quel déclencheur, vous pouvez utiliser une ou plusieurs conditions pour déterminer si le workflow doit s’exécuter ou non. Par exemple :  

```json
"dailyReport" : {
    "type": "recurrence",
    "conditions": [ {
        "expression": "@parameters('sendReports')"
    } ],
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

Dans ce cas, le rapport se déclenche uniquement lorsque le paramètre `sendReports` du workflow est défini sur true. Enfin, les conditions peuvent faire référence au code d’état du déclencheur. Par exemple, vous pouvez lancer un workflow uniquement lorsque votre site web renvoie un code d’état 500, comme suit :
  
```  
"conditions": [  
        {  
          "expression": "@equals(triggers().code, 'InternalServerError')"  
        }  
      ]  
```  
  
> [!NOTE]  
> Lorsqu’une expression fait référence au code d’état du déclencheur \(de quelque façon\), le comportement par défaut \(trigger only on 200 \(OK\) \) est remplacé. Par exemple, si vous souhaitez que le déclenchement soit effectué avec le code d’état 200 et le code d’état 201, vous devez inclure la condition `@or(equals(triggers().code, 200),equals(triggers().code,201))`.  
  
## <a name="start-multiple-runs-for-a-request"></a>Démarrer plusieurs exécutions pour une requête

Pour lancer plusieurs exécutions pour une requête unique, par exemple lorsque vous souhaitez interroger un point de terminaison qui peut présenter plusieurs nouveaux éléments entre les intervalles d’interrogation, vous pouvez utiliser `splitOn`.
  
Avec `splitOn`, vous spécifiez la propriété à l’intérieur de la charge utile de réponse qui contient le tableau de tous les éléments que vous souhaitez utiliser pour démarrer l’exécution du déclencheur. Par exemple, supposons que vous ayez une API qui renvoie la réponse suivante :  
  
```json
{
    "Status" : "success",
    "Rows" : [
        {  
            "id" : 938109380,
            "name" : "mycoolrow"
        },
        {
            "id" : 938109381,
            "name" : "another row"
        }
    ]
}
```
  
Comme votre application logique a uniquement besoin du contenu de Rows, vous pouvez construire votre déclencheur comme dans cet exemple :  
  
```json
"mysplitter" : {
    "type" : "http",
    "recurrence": {
        "frequency": "Minute",
        "interval": "1"
    },
    "intputs" : {
        "uri" : "https://mydomain.com/myAPI",
        "method" : "GET"
    },
    "splitOn" : "@triggerBody()?.Rows"
}
```
  
Ensuite, dans la définition du workflow, `@triggerBody().name` renvoie `mycoolrow` pour la première exécution, et `another row` pour la deuxième exécution. Les sorties du déclencheur se présentent comme dans cet exemple :  
  
```json
{
    "body" : {
        "id" : 938109381,
        "name" : "another row"
    }
}
```

Par conséquent, si vous utilisez `SplitOn`, vous ne pouvez pas obtenir les propriétés qui ne se trouvent pas dans le tableau (dans le cas présent, le champ `Status`).  
  
> [!NOTE]  
> Dans cet exemple, nous utilisons l’opérateur `?` afin d’éviter un échec en cas d’absence de la propriété `Rows`. 
  
## <a name="single-run-instance"></a>Instance d’exécution unique

Vous pouvez configurer les déclencheurs présentant une propriété recurrence de telle sorte qu’ils ne se déclenchent que lorsque toutes les exécutions actives sont terminées. Si une répétition planifiée a lieu alors qu’une exécution est encore en cours, le déclencheur attend l’intervalle de périodicité planifié suivant pour effectuer une nouvelle vérification.

Vous pouvez configurer ce paramètre via les options d’opération :

```json
"triggers": {
    "mytrigger": {
        "type": "http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="types-and-inputs"></a>Types et entrées  

Il existe de nombreux types d’actions, chacune présentant un comportement unique. Les actions de collection peuvent contenir de nombreuses autres actions.

### <a name="standard-actions"></a>Actions standard  

-   **HTTP** - Cette action appelle un point de terminaison web HTTP.  
  
-   **ApiConnection** \- Cette action se comporte comme l’action HTTP, mais utilise les API gérées par Microsoft.  
  
-   **ApiConnectionWebhook** \- Similaire à HTTPWebhook, mais utilise les API gérées par Microsoft.  
  
-   **Response** \- Cette action définit une réponse pour un appel entrant.  
  
-   **Wait** \- Cette action simple déclenche une attente pendant une durée définie ou jusqu’à un moment spécifique.  
  
-   **Workflow** \- Cette action représente un workflow imbriqué.  

### <a name="collection-actions"></a>Actions de collection

-   **Scope** \- Cette action est un regroupement logique d’autres actions.

-   **Condition** \- Cette action évalue une expression et exécute la branche de résultat correspondante.

-   **ForEach** \- Cette action en boucle effectue une itération dans un tableau et exécute des actions internes pour chaque élément.

-   **Until** \- Cette action en boucle exécute des actions internes jusqu’à ce qu’une condition soit vérifiée.
  
Chaque type d’action présente un ensemble différent d’**entrées** qui définissent le comportement d’une action.  
  
## <a name="http-action"></a>Action HTTP  

Les actions HTTP appellent un point de terminaison spécifique et vérifient la réponse pour déterminer si le workflow doit s’exécuter. L’objet d’**entrée** prend l’ensemble des paramètres requis pour construire l’appel HTTP :  
  
|Nom de l'élément|Requis|Type|Description|  
|----------------|------------|--------|---------------|  
|statique|Oui|String|Peut être l’une des méthodes HTTP suivantes : **GET**, **POST**, **PUT**, **DELETE**, **PATCH** ou **HEAD**.|  
|URI|Oui|String|Point de terminaison HTTP ou HTTPS qui est appelé. La longueur maximale est de 2 Ko.|  
|requêtes|Non|Object|Représente les paramètres de requête à ajouter à l’URL. Par exemple, `"queries" : { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL.|  
|headers|Non|Object|Représente chacun des en-têtes envoyés à la requête. Par exemple, pour définir la langue et le type sur une requête :`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|Non|Object|Représente la charge utile envoyée au point de terminaison.|  
|retryPolicy|Non|Object|Permet de personnaliser le comportement de nouvelle tentative pour les erreurs 4xx ou 5xx.|  
|operationsOptions|Non|String|Définit l’ensemble de comportements spéciaux à substituer.|  
|authentication|Non|Object|Représente la méthode à utiliser pour authentifier la requête. Pour plus d’informations sur cet objet, consultez [Authentification sortante de Scheduler](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication). En plus de Scheduler, une autre propriété est prise en charge : `authority`. Par défaut, cette propriété est définie sur `https://login.windows.net` lorsqu’aucune valeur n’est spécifiée, mais vous pouvez utiliser une autre audience comme `https://login.windows\-ppe.net`|  
  
Les actions HTTP \(et ApiConnection\) prennent en charge les stratégies de nouvelle tentative. Une stratégie de nouvelle tentative s’applique aux échecs temporaires, caractérisés par les codes d’état HTTP 408, 429 et 5xx, en plus de toutes les exceptions de connectivité. Cette stratégie est décrite à l’aide de l’objet *retryPolicy* défini comme suit :
  
```json
"retryPolicy" : {
    "type": "<type-of-retry-policy>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```
  
L’intervalle avant nouvelle tentative est spécifié dans le format ISO 8601. L’intervalle présente une valeur par défaut (et minimale) de 20 secondes, tandis que la valeur maximale est d’une heure. Le nombre de nouvelles tentatives par défaut (et maximal) est de 4. Si la définition de stratégie de nouvelle tentative n’est pas spécifiée, une stratégie `fixed` est utilisée avec les valeurs d’intervalle et de nombre de nouvelles tentatives par défaut. Pour désactiver la stratégie de nouvelle tentative, définissez son type sur `None`.  
  
Par exemple, en cas d’échec temporaire, l’action suivante tente à nouveau de récupérer les dernières actualités deux fois, pour un total de 3 exécutions, avec un délai de 30 secondes entre chaque tentative :  
  
```json
"latestNews" : {
    "type": "http",
    "inputs": {
        "method": "GET",
        "uri": "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```
### <a name="asynchronous-patterns"></a>Modèles asynchrones

Par défaut, toutes les actions basées sur HTTP prennent en charge le modèle d’opération asynchrone standard. Par conséquent, si le serveur distant indique que la requête est acceptée pour traitement avec une réponse 202 \(Accepted\) (Acceptée), le moteur Logic Apps continue à interroger l’URL spécifiée dans l’en-tête Location de la réponse jusqu’à atteindre un état terminal \(c’est-à-dire une réponse autre que 202\).  
  
Pour désactiver le comportement asynchrone décrit précédemment, définissez une option `DisableAsyncPattern` dans les entrées de l’action. Dans ce cas, la sortie de l’action est basée sur la réponse 202 initiale du serveur.  
  
```json
"invokeLongRunningOperation" : {
    "type": "http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

#### <a name="asynchronous-limits"></a>Limites asynchrones

Un modèle asynchrone peut être limité dans sa durée à un intervalle de temps spécifique.  Si l’intervalle de temps s’écoule sans qu’un état terminal ait été atteint, l’état de l’action sera marqué comme `Cancelled` avec un code `ActionTimedOut`.  Le délai d’expiration de la limite est spécifié au format ISO 8601.  Les limites peuvent être spécifiées avec la syntaxe suivante :

``` json
"<action-name>": {
    "type": "workflow|webhook|http|apiconnectionwebhook|apiconnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="api-connection"></a>ApiConnection  

ApiConnection est une action qui fait référence à un connecteur géré par Microsoft.
Cette action requiert une référence à une connexion valide, ainsi que des informations sur l’API et les paramètres requis.

|Nom de l'élément|Requis|Type|Description|  
|----------------|------------|--------|---------------|  
|host|Oui|Object|Représente les informations sur le connecteur, telles que la propriété runtimeUrl et la référence à l’objet de connexion.|
|statique|Oui|String|Peut être l’une des méthodes HTTP suivantes : **GET**, **POST**, **PUT**, **DELETE**, **PATCH** ou **HEAD**.|  
|path|Oui|String|Chemin de l’opération d’API.|  
|requêtes|Non|Object|Représente les paramètres de requête à ajouter à l’URL. Par exemple, `"queries" : { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL.|  
|headers|Non|Object|Représente chacun des en-têtes envoyés à la requête. Par exemple, pour définir la langue et le type sur une requête :`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|Non|Object|Représente la charge utile envoyée au point de terminaison.|  
|retryPolicy|Non|Object|Permet de personnaliser le comportement de nouvelle tentative pour les erreurs 4xx ou 5xx.|  
|operationsOptions|Non|String|Définit l’ensemble de comportements spéciaux à substituer.|  

```json
"Send_Email": {
    "type": "apiconnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "post",
        "body": {
            "Subject": "New Tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
    }
```

## <a name="api-connection-webhook-action"></a>Action ApiConnectionWebhook

```json
"Send_approval_email": {
    "type": "apiconnectionwebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

Les limites d’une action webhook peuvent être spécifiées de la même manière que les [limites asynchrones HTTP](#asynchronous-limits).
  
## <a name="response-action"></a>Action de réponse  

Ce type d’action contient la charge utile de réponse entière d’une requête HTTP et inclut des entrées statusCode, body et headers :  
  
```json
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "runAfter": {}
}
```
  
L’action response présente des restrictions spéciales qui ne s’appliquent pas aux autres actions. Plus précisément :  
  
-   Les actions response ne peuvent pas être parallèles dans une définition, car une réponse déterministe à la requête entrante est requise.  
  
-   Si une action response est atteinte après que la requête entrante a reçu une réponse, l’action est considérée comme ayant échoué \(conflict\) (conflit), et par conséquent, l’exécution est définie sur `Failed` (Échec).  
  
-   Un workflow comportant des actions response ne peut pas avoir `splitOn` dans son déclencheur, car un seul appel entraîne de nombreuses exécutions. Par conséquent, une validation est requise lorsque le flux est PUT et cause une requête incorrecte.  
  
## <a name="wait-action"></a>Action wait  

L’action `wait` interrompt l’exécution du workflow pendant l’intervalle spécifié. Par exemple, pour définir une attente de 15 minutes, vous pouvez utiliser cet extrait de code :  
  
```json
"waitForFifteenMinutes" : {
    "type": "wait",
    "inputs": {
        "interval": {
            "unit" : "minute",
            "count" : 15
        }
    }
}
```  
  
Pour attendre jusqu’à un moment spécifique, vous pouvez utiliser cet exemple :  
  
```json
"waitUntilOctober" : {
    "type": "wait",
    "inputs": {
        "until": {
            "timestamp" : "2016-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> La durée d’attente peut être spécifiée soit à l’aide de l’objet **interval**, soit à l’aide de l’objet **until**.  
  
|Nom|Requis|Type|Description|  
|--------|------------|--------|---------------|  
|interval|Non|Object|Temps d’attente basé sur une durée.|  
|interval unit|Oui|String|Un de ces intervalles : second (seconde), minute (minute), hour (heure), day (jour), week (semaine), month (mois), year (année).|  
|interval count|Oui|String|Temps d’attente basé sur l’unité interne donnée.|  
|until|Non|Object|Temps d’attente basé sur un moment spécifique.|  
|until timestamp|Oui|String|Moment auquel l’attente expirera (exprimé en heure UTC).|  

## <a name="query-action"></a>Action de requête

L’action `query` vous permet de filtrer un tableau en fonction d’une condition. Par exemple, pour sélectionner des nombres supérieurs à 2, vous pouvez utiliser :

```json
"FilterNumbers" : {
    "type": "query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

La sortie de l’action `query` est un tableau qui contient les éléments du tableau d’entrée qui remplissent la condition.

> [!NOTE]
> Si aucune valeur ne remplit la condition `where`, le résultat est un tableau vide.

|Nom|Requis|Type|Description|
|--------|------------|--------|---------------|
|from|Oui|Tableau|Tableau source.|
|où|Oui|String|Condition à appliquer à chaque élément du tableau source.|

## <a name="terminate-action"></a>Action terminate

L’action terminate met fin à l’exécution de l’exécution du workflow, abandonnant les actions en cours et ignorant les actions restantes. Par exemple, pour mettre fin à une exécution qui présente l’état **Failed** (Échec), vous pouvez utiliser l’extrait de code suivant :

```json
"HandleUnexpectedResponse" : {
    "type": "terminate",
    "inputs": {
        "runStatus" : "failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response.",
        }
    }
}
```

> [!NOTE]
> Les actions déjà terminées ne sont pas affectées par l’action terminate.

|Nom|Requis|Type|Description|
|--------|------------|--------|---------------|
|runStatus|Oui|String|État d’exécution cible. Peut être défini sur **Failed** (Échec) ou **Cancelled** (Annulée).|
|runError|Non|Object|Détails de l’erreur. Uniquement pris en charge lorsque **runStatus** est défini sur **Failed** (Échec).|
|runError code|Non|String|Code d’erreur de l’exécution.|
|runError message|Non|String|Message d’erreur de l’exécution.|

## <a name="compose-action"></a>Action compose

L’action compose vous permet de construire un objet arbitraire. La sortie de l’action compose est le résultat de l’évaluation de ses entrées. Par exemple, vous pouvez utiliser l’action compose pour fusionner les sorties de plusieurs actions :

```json
"composeUserRecord" : {
    "type": "compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
        }
    }
}
```

> [!NOTE]
> L’action **compose** peut être utilisée pour construire n’importe quelle sortie, y compris des objets, des tableaux et tout autre type pris en charge de façon native par les applications logiques, tel que XML et binaire.

## <a name="workflow-action"></a>Action workflow   

|Nom|Requis|Type|Description|  
|--------|------------|--------|---------------|  
|host id|Oui|String|ID de ressource du workflow que vous souhaitez appeler.|  
|host triggerName|Oui|String|Nom du déclencheur que vous souhaitez appeler.|  
|requêtes|Non|Object|Représente les paramètres de requête à ajouter à l’URL. Par exemple, `"queries" : { "api-version": "2015-02-01" }` ajoute `?api-version=2015-02-01` à l’URL.|  
|headers|Non|Object|Représente chacun des en-têtes envoyés à la requête. Par exemple, pour définir la langue et le type sur une requête :`"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|Non|Object|Représente la charge utile envoyée au point de terminaison.|  
  
```json
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName " : "mytrigger001"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
    }
```
  
Un contrôle d’accès est effectué sur le workflow \(plus précisément, sur le déclencheur\), ce qui signifie que vous avez besoin d’un accès au workflow.  
  
Les sorties de l’action `workflow` sont basées sur ce que vous avez défini pour l’action `response` dans le workflow enfant. Si vous n’avez pas défini d’action `response`, les sorties sont vides.  

## <a name="collection-actions-scopes-and-loops"></a>Actions de collection (étendues et boucles)

Certains types d’actions peuvent contenir d’autres actions. Il est possible de faire référence aux actions d’une collection directement en dehors de la collection. Si vous avez défini `http` dans une étendue (scope), `@body('http')` est toujours valide n’importe où dans un workflow. Les actions d’une collection peuvent uniquement être exécutées après (avec la propriété `runAfter`) les actions de la même collection.

## <a name="scope-action"></a>Action scope

L’action `scope` vous permet de logiquement regrouper les actions d’un flux de travail.

|Nom|Requis|Type|Description|  
|--------|------------|--------|---------------|  
|actions|Oui|Object|Actions internes à exécuter dans l’étendue.|

```json
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

## <a name="foreach-action"></a>Action foreach

Cette action en boucle effectue une itération dans un tableau et exécute des actions internes pour chaque élément. Par défaut, la boucle foreach exécute les itérations en parallèle (20 exécutions en parallèle à la fois). Vous pouvez définir les règles d’exécution à l’aide du paramètre `operationOptions`.

|Nom|Requis|Type|Description|  
|--------|------------|--------|---------------|  
|actions|Oui|Object|Actions internes à exécuter dans la boucle.|
|foreach|Oui|string|Tableau dans lequel effectuer l’itération.|
|operationOptions|no|string|Toutes les options d’opération pour le comportement. Pour le moment, prend uniquement en charge `sequential` pour exécuter les itérations de manière séquentielle (le comportement par défaut est parallèle)|

```json
"forEach_email": {
    "type": "foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter":{
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Action until

Cette action en boucle exécute des actions internes jusqu’à ce qu’une condition soit vérifiée.

|Nom|Requis|Type|Description|  
|--------|------------|--------|---------------|  
|actions|Oui|Object|Actions internes à exécuter dans la boucle.|
|expression|Oui|string|Expression à évaluer après chaque itération.|
|limit|yes|Object|Limites de la boucle (au moins une limite doit être définie).|
|count|no|int|Nombre maximal d’itérations pouvant être exécutées.|
|timeout|no|string|Délai d’expiration de la boucle.  Format ISO 8601|


```json
 "Until_succeeded": {
    "actions": {
        "Http": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {},
    "type": "Until"
}
```

## <a name="conditions---if-action"></a>Conditions : action If

L’action `If` vous permet d’évaluer une condition et d’exécuter une branche spécifique si l’expression évaluée présente la valeur `true`.

|Nom|Requis|Type|Description|  
|--------|------------|--------|---------------|  
|actions|Oui|Object|Actions internes à exécuter lorsque l’expression évaluée présente la valeur `true`.|
|expression|Oui|string|Expression à évaluer.|
|else|no|Object|Actions internes à exécuter lorsque l’expression évaluée présente la valeur `false`.|
  
```json
"My_condition": {
    "actions": {
        "If_true": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {},
                "type": "Http"
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {},
    "type": "If"
}
```  
  
Le tableau suivant montre comment les conditions peuvent utiliser des expressions dans une action à travers plusieurs exemples :  
  
|Valeur JSON|Résultat|  
|--------------|----------|  
|`"expression": "@parameters('hasSpecialAction')"`|Cette condition est remplie dès lors que l’expression évaluée présente la valeur true. Seules les expressions booléennes sont prises en charge. Pour convertir d’autres types en valeurs booléennes, utilisez les fonctions `empty`, `equals`.|  
|`"expression": "@greater(actions('act1').output.value, parameters('threshold'))"`|Les fonctions de comparaison sont prises en charge. Pour cet exemple, l’action s’exécute uniquement lorsque la sortie d’act1 est supérieure au seuil.|  
|`"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"`|Les fonctions logiques sont également prises en charge pour créer des expressions booléennes imbriquées. Dans le cas présent, l’action s’exécute lorsque la sortie d’act1 est supérieure au seuil ou inférieure à 100.|  
|`"expression": "@equals(length(actions('act1').outputs.errors), 0))"`|Vous pouvez utiliser les fonctions de tableau pour vérifier si un tableau contient des éléments. Dans le cas présent, l’action s’exécute lorsque le tableau d’erreurs est vide.| 
|`"expression": "parameters('hasSpecialAction')"`|Erreur : cette condition n’est pas valide, car @ est requis pour les conditions.|  
  
Si une condition est évaluée avec succès, elle est marquée comme `Succeeded` (Réussite). Les actions évaluées au sein des objets `actions` ou `else` présentent la valeur `Succeeded` (Réussite) lorsque leur exécution réussit, `Failed` (Échec) lorsque leur exécution échoue ou `Skipped` (Ignorée) lorsque cette branche n’est pas exécutée.

## <a name="next-steps"></a>Étapes suivantes

[API REST du service de workflow](https://docs.microsoft.com/rest/api/logic/workflows)
