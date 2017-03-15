---
title: "Appeler, déclencher ou imbriquer des applications logiques via des points de terminaison HTTP - Azure Logic Apps | Microsoft Docs"
description: "Ajouter et configurer des points de terminaison HTTP pour appeler, déclencher ou imbriquer des workflows pour des applications logiques dans Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 8c4cb0aefcd7241b17c6afcf33496c1fc2e6f675
ms.lasthandoff: 03/03/2017

---

# <a name="add-http-endpoints-to-call-trigger-or-nest-logic-app-workflows"></a>Ajouter et configurer des points de terminaison HTTP pour appeler, déclencher ou imbriquer des workflows pour des applications logiques

Les applications logiques peuvent exposer en mode natif les points de terminaison HTTP synchrone en tant que déclencheurs afin que vous puissiez appeler vos applications logiques. Vous pouvez également utiliser un modèle de points de terminaison appelables pour appeler des applications logiques sous forme de workflows imbriqués en ajoutant l’action **Choisir un workflow Logic Apps** à votre application logique.

Vous pouvez utiliser ces déclencheurs pour la réception des requêtes :

* Demande
* ApiConnectionWebhook
* HttpWebhook

Cette rubrique utilise le déclencheur **Requête** comme exemple, mais tous les principes s’appliquent de façon identique aux autres types de déclencheurs.

## <a name="add-a-trigger-to-your-logic-app-definition"></a>Ajouter un déclencheur à votre définition d’application logique

1. Dans le concepteur d’application logique, ajoutez un déclencheur qui peut recevoir des requêtes entrantes à votre définition d’application logique. Par exemple, ajoutez le déclencheur **Requête** à votre application logique.

2.    Sous **Schéma JSON du corps de la requête**, vous pouvez entrer un schéma JSON pour la charge utile que vous pensez recevoir. Si vous n’avez pas un schéma prêt, vous pouvez choisir **Utiliser l’exemple de charge utile pour générer le schéma** pour générer un schéma JSON à partir d’un exemple de charge utile.

    Le concepteur utilise ce schéma pour générer des jetons qui vous permettent d’utiliser, d’analyser et de passer des données à partir du déclencheur manuel via votre workflow.

    ![Ajouter l’action de la requête][2]

2.    Après avoir enregistré votre définition d’application logique, sous **HTTP POST pour cette URL**, vous obtenez une URL de rappel générée, comme dans cet exemple :

    ``` text
    https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
    ```

    Cette URL contient une clé de signature d’accès partagé (SAP) dans les paramètres de requête utilisés pour l’authentification. 
    Vous pouvez également obtenir ce point de terminaison auprès du portail Azure :

    ![Point de terminaison de l’URL][1]

    Vous pouvez aussi appeler :

    ``` text
    POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
    ```

### <a name="change-the-http-method-for-your-trigger"></a>Modifier la méthode HTTP pour votre déclencheur

Par défaut, le déclencheur Requête attend une requête HTTP POST. Pour configurer une autre méthode HTTP, choisissez **Afficher les options avancées**.

> [!NOTE]
> Un seul type de méthode est autorisé.

### <a name="customize-the-relative-trigger-url"></a>Personnaliser l’URL relative du déclencheur

Vous pouvez également personnaliser le chemin relatif de l’URL de la requête pour qu’elle accepte des paramètres.

1. Sur le déclencheur **Requête**, choisissez **Afficher les options avancées**. Sous **Chemin relatif**, entrez `customer/{customerId}`.

    ![Déclencheur d’URL relative](./media/logic-apps-http-endpoint/relativeurl.png)

2.    Pour utiliser le paramètre, mettez à jour l’action **Réponse**.

    *    Vous voyez normalement apparaître `customerId` dans le sélecteur de jetons.
    *    Pour retourner `Hello {customerId}`, mettez à jour le corps de l’action **Réponse**.

    ![Réponse d’URL relative](./media/logic-apps-http-endpoint/relativeurlresponse.png)

3. Enregistrez votre application logique. L’URL de la requête est mise à jour avec le chemin relatif.

4. Copiez la nouvelle URL de la requête et collez-la dans une nouvelle fenêtre de navigateur. Remplacez `{customerId}` par `123`, puis appuyez sur Entrée.

    Ce texte doit être retourné :`Your customer Id is 123`

### <a name="security-for-the-trigger-url"></a>Sécurité pour l’URL du déclencheur

Les URL de rappel de l’application logique Logic Apps sont générées de façon sécurisée via une signature d’accès partagé. La signature est passée directement comme paramètre de requête et doit être validée avant que votre application logique puisse être déclenchée. Elle est générée via la combinaison unique d’une clé secrète par application logique, du nom du déclencheur et de l’opération qui est effectuée. Sauf si quelqu’un a accès à la clé secrète de l’application logique, il ne peut pas générer une signature valide.

## <a name="call-your-logic-app-triggers-endpoint"></a>Appeler le point de terminaison du déclencheur de votre application logique

Une fois que vous avez créé le point de terminaison pour votre déclencheur, vous pouvez déclencher votre application logique via un `POST` vers l’URL complète. Vous pouvez inclure plus d’en-têtes ainsi que du contenu dans le corps. Si le type de contenu est `application/json` , vous pouvez référencer des propriétés depuis l’intérieur de la requête. Dans le cas contraire, il est considéré comme une unité binaire unique qui peut être passée à d’autres API, mais qui ne peut pas faire l’objet d’un référencement interne au workflow sans conversion du contenu. Par exemple, si vous passez le contenu `application/xml`, vous pouvez utiliser `@xpath()` pour une extraction XPath, ou `@json()` pour effectuer la conversion de XML vers JSON. Découvrez plus en détail comment [utiliser les types de contenu](../logic-apps/logic-apps-content-type.md).

Vous pouvez aussi spécifier un schéma JSON dans la définition. Ce schéma entraîne la génération de jetons par le concepteur. Vous pouvez ensuite passer ces jetons dans des étapes. L’exemple suivant permet de rendre les jetons `title` et `name` disponibles dans le concepteur :

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

## <a name="reference-the-content-from-the-incoming-request"></a>Référencer le contenu depuis la requête entrante

La fonction `@triggerOutputs()` génère une sortie du contenu de la requête entrante. Le résultat ressemble à ceci :

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

Pour accéder à la propriété `body`, vous pouvez utiliser le raccourci `@triggerBody()`. 

## <a name="respond-to-the-request"></a>Répondre à la requête

Pour certaines requêtes qui démarrent une application logique, vous pouvez répondre à l’appelant avec du contenu. Pour construire le code d’état, le corps et les en-têtes de votre réponse, vous pouvez utiliser un type d’action appelé **Réponse**. Cependant, si aucune **Réponse** n’est incluse, le point de terminaison de l’application logique répond *immédiatement* avec **202 - Accepté**.

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

Les réponses ont ces propriétés :

| Propriété | Description |
| --- | --- |
| statusCode |Code d’état HTTP pour répondre à la requête entrante. Ce code peut être tout code d’état valide commençant par 2xx, 4xx ou 5xx. Cependant, les codes d’état 3xx ne sont pas autorisés. |
| body |Un objet corps peut être une chaîne, un objet JSON ou même du contenu binaire référencé à partir d’une étape précédente. |
| headers |Vous pouvez définir un nombre quelconque d’en-têtes à inclure dans la réponse. |

Dans votre application logique, toutes les étapes nécessaires à la réponse doivent être effectuées dans les *60 secondes* pour que la requête d’origine obtienne la réponse, * sauf si vous appelez le workflow comme application logique imbriquée*. Si aucune réponse n’est produite dans les 60 secondes, la requête entrante dépasse le délai d’expiration et reçoit une réponse HTTP **408 - Dépassement du délai d’expiration par le client**. Pour les applications logiques imbriquées, l’application logique parente continue à attendre une réponse jusqu’à ce qu’elle se termine, quelle que soit la durée de l’opération.

## <a name="advanced-endpoint-configuration"></a>Configuration avancée du point de terminaison

Les applications logiques incluent la prise en charge du point de terminaison d’accès direct et utilisent toujours la méthode `POST` pour démarrer l’exécution de l’application logique. Auparavant, l’application API **Écouteur HTTP** prenait déjà en charge la modification des segments d’URL et de la méthode HTTP. Vous pouvez même renforcer la sécurité ou configurer un domaine personnalisé en l’ajoutant à l’hôte d’application API (l’application web hébergeant l’application API). 

Cette fonctionnalité est disponible via **Gestion des API** :

* [Modification de la méthode de la requête](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Modification des segments d’URL de la requête](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configurez vos domaines de gestion des API sous l’onglet **Configurer** du portail Azure Classic.
* Configurez une stratégie pour rechercher l’authentification de base.

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Résumé de la migration à partir de 2014-12-01-preview

| 2014-12-01-preview | 2016-06-01 |
| --- | --- |
| Cliquez sur l’application API **Écouteur HTTP** |Cliquez sur **Déclenchement manuel** (aucune application API nécessaire) |
| Paramètre d’écouteur HTTP «*Envoie une réponse automatiquement*» |Incluez ou non une action **Réponse** dans la définition du workflow |
| Configurer l’authentification de base ou OAuth |via la gestion des API |
| Configurer la méthode HTTP |via la gestion des API |
| Configurer le chemin d’accès relatif |via la gestion des API |
| Référencer le corps entrant par le biais de `@triggerOutputs().body.Content` |Référencer par le biais de `@triggerOutputs().body` |
| **Envoyer une réponse HTTP** sur l’écouteur HTTP |Cliquez sur **Répondre à la requête HTTP** (aucune application API nécessaire) |

[1]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[2]: ./media/logic-apps-http-endpoint/manualtrigger.png
[3]: ./media/logic-apps-http-endpoint/response.png

