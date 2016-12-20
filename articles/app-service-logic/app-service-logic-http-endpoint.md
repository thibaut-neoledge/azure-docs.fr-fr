---
title: "Applications logiques en tant que points de terminaison pouvant être appelés"
description: "Comment créer et configurer des points de terminaison déclencheurs et les utiliser dans une application logique au sein d’Azure App Service"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 372114b1420139bdf525521a427f924aae38db6f


---
# <a name="logic-apps-as-callable-endpoints"></a>Applications logiques en tant que points de terminaison pouvant être appelés
Les applications logiques peuvent exposer un point de terminaison HTTP synchrone en mode natif en tant que déclencheur.  Vous pouvez également utiliser le modèle de points de terminaison pouvant être appelés pour appeler les applications logiques sous la forme d’un flux de travail imbriqué, via l’action « Workflow » d’une application logique.

Il existe 3 types de déclencheurs qui peuvent recevoir des requêtes :

* Demande
* ApiConnectionWebhook
* HttpWebhook

Dans le reste de cet article, nous utiliserons **request** comme exemple. Toutefois, tous les principes s’appliquent de la même manière aux 2 autres types de déclencheurs.

## <a name="adding-a-trigger-to-your-definition"></a>Ajout d’un déclencheur à votre définition
La première étape consiste à ajouter à la définition de votre application logique un déclencheur qui peut recevoir des requêtes entrantes.  Dans le concepteur, vous pouvez rechercher « demande HTTP » pour ajouter la carte de déclencheur. Vous pouvez définir un schéma JSON de corps de requête ; le concepteur génère des jetons pour vous aider à analyser et transmettre des données à partir du déclenchement manuel, via le flux de travail.  Je recommande un outil tel que [jsonschema.net](http://jsonschema.net) pour générer un schéma JSON à partir d’un exemple de charge utile du corps.

![Carte de déclencheur de requête][2]

Une fois la définition de l’application logique enregistrée, une URL de rappel est générée. Elle est semblable à celle-ci :

``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Cette URL contient une clé SAS dans les paramètres de requête utilisés pour l’authentification.

Vous pouvez également obtenir ce point de terminaison dans le portail Azure :

![][1]

Ou, en appelant :

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Sécurité pour l’URL du déclencheur
Les URL de rappel Logic Apps sont générés de manière sécurisée via une signature d’accès partagé.  La signature est transmise comme paramètre de requête et doit être validée avant que l’application logique se déclenche.  Elle est générée par une combinaison unique de clé secrète par application logique, le nom du déclencheur et l’opération en cours d’exécution.  Sauf si un utilisateur dispose d’un accès à la clé secrète de l’application logique, ils ne seraient pas en mesure de générer une signature valide.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Appel du point de terminaison du déclencheur d’application logique
Une fois que vous avez obtenu le point de terminaison de votre déclencheur, vous pouvez le déclencher par le biais d’un élément `POST` dans l’URL complète. Vous pouvez inclure des en-têtes supplémentaires ainsi que du contenu dans le corps.

Si le type de contenu est `application/json` , vous pouvez référencer des propriétés depuis l’intérieur même de la requête. Dans le cas contraire, il est considéré comme une unité binaire unique qui peut être transmise à d’autres API, mais qui ne peut pas faire l’objet d’un référencement interne au workflow sans conversion du contenu.  Par exemple, si vous passez le contenu de l’élément `application/xml`, vous pouvez utiliser l’élément `@xpath()` pour effectuer une extraction xpath, ou `@json()` pour effectuer la conversion de XML vers JSON.  Vous trouverez plus d’informations sur l’utilisation des types de contenu [ici](app-service-logic-content-type.md)

En outre, vous pouvez spécifier un schéma JSON dans la définition. Cela entraîne la génération de jetons par le concepteur. Vous pouvez ensuite passer ces jetons via des étapes.  Le code suivant permet de rendre les jetons `title` et `name` disponibles dans le concepteur :

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Référencement du contenu de la requête entrante
La fonction `@triggerOutputs()` génère le contenu de la requête entrante. Par exemple, elle peut se présenter comme suit :

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Vous pouvez utiliser le raccourci `@triggerBody()` pour accéder spécifiquement à la propriété `body`. 

## <a name="responding-to-the-request"></a>Réponse à la requête
Pour certaines requêtes qui démarrent une application logique, vous pouvez répondre à l’appelant avec du contenu. Il existe un nouveau type d’action appelé **response** , qui peut être utilisé pour construire le code d’état, le corps et les en-têtes de votre réponse. Notez que si aucune forme de **réponse** n’est présente, le point de terminaison d’application logique répondra *immédiatement* avec **202 Accepted**.

![Action de réponse HTTP][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Les réponses ont les propriétés suivantes :

| Propriété | Description |
| --- | --- |
| statusCode |Code d’état HTTP pour répondre à la requête entrante. Ce peut être tout code d’état valide commençant par 2xx, 4xx ou 5xx. Les codes d’état 3xx ne sont pas autorisés. |
| body |Un objet corps peut être une chaîne, un objet JSON ou même du contenu binaire référencé à partir d’une étape précédente. |
| headers |Vous pouvez définir n’importe quel nombre d’en-têtes à inclure dans la réponse. |

Toutes les étapes de l’application logique qui sont requises pour la réponse doivent prendre au maximum *60 secondes* pour que la requête d’origine puisse recevoir la réponse, **sauf si le workflow est appelé en tant qu’application logique imbriquée**. Si aucune action de réponse n’est atteinte en 60 secondes, la requête entrante expire et reçoit la réponse HTTP **408 Délai d’expiration du client** .  Pour les applications logiques imbriquées, l’application logique parente continue à attendre une réponse jusqu’à la fin, quelle que soit la durée de l’opération.

## <a name="advanced-endpoint-configuration"></a>Configuration avancée du point de terminaison
Les applications logiques incluent la prise en charge du point de terminaison d’accès direct et utilisent systématiquement la méthode `POST` pour démarrer l’exécution de l’application logique. En outre, l’application API **Écouteur HTTP** prenait déjà en charge la modification des segments d’URL et de la méthode HTTP. Vous pouviez même renforcer la sécurité ou configurer un domaine personnalisé en l’ajoutant à l’hôte d’application API (l’application web hébergeant l’application API). 

Cette fonctionnalité est disponible par le biais du service **Gestion des API**:

* [Modification de la méthode de la requête](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Modification des segments d’URL de la requête](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configuration de vos domaines de gestion des API dans l’onglet **Configurer** du portail Azure Classic
* Configuration d’une stratégie pour vérifier l’authentification de base (**lien nécessaire**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Résumé de la migration à partir de 2014-12-01-preview
| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| Cliquer sur l’application API **Écouteur HTTP** |Cliquer sur **Déclenchement manuel** (aucune application API requise) |
| Paramètre d’écouteur HTTP «*Envoie une réponse automatiquement*» |Inclure ou non une action **response** dans la définition de workflow |
| Configurer l’authentification de base ou OAuth |Par le biais de la gestion des API |
| Configurer la méthode HTTP |Par le biais de la gestion des API |
| Configurer le chemin d’accès relatif |Par le biais de la gestion des API |
| Référencer le corps entrant par le biais de `@triggerOutputs().body.Content` |Référencer par le biais de `@triggerOutputs().body` |
| **Envoyer une réponse HTTP** sur l’écouteur HTTP |Cliquer sur **Répondre à la requête HTTP** (aucune application API requise) |

[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png



<!--HONumber=Nov16_HO3-->


