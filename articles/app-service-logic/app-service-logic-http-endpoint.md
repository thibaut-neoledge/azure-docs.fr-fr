<properties
   pageTitle="Applications logiques en tant que points de terminaison pouvant être appelés"
   description="Comment créer et configurer l’écouteur HTTP et l’utiliser dans une application logique d’Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/05/2016"
   ms.author="jehollan"/>


# Applications logiques en tant que points de terminaison pouvant être appelés

La version précédente du schéma des applications logiques (*2014-12-01-preview*) exigeait une application API appelée **écouteur HTTP** pour exposer un point de terminaison HTTP pouvant être appelé de façon synchrone. Avec le dernier schéma (*2015-08-01-preview*), les applications logiques peuvent exposer un point de terminaison HTTP synchrone en mode natif.

## Ajout d’un déclencheur à votre définition
La première étape consiste à ajouter à la définition de votre application logique un déclencheur qui peut recevoir des requêtes entrantes. Il existe 3 types de déclencheurs qui peuvent recevoir des requêtes :
* manual
* apiConnectionWebhook
* httpWebhook

Dans le reste de cet article, nous utiliserons **manual** comme exemple, mais tous les principes s’appliquent de la même manière aux deux autres types de déclencheurs. Voici comment ajouter ce déclencheur à votre définition de flux de travail :

```
{
    "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "triggers" : {
        "myendpointtrigger" : {
            "type" : "manual"
        }
    }
}
```

Cette opération crée un point de terminaison que vous pouvez appeler dans une URL comme suit :
 
```
https://prod-03.brazilsouth.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?...
```

Vous pouvez obtenir ce point de terminaison à cet endroit dans l’interface utilisateur :

![][1]

Ou, en appelant :

```
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

## Appel du point de terminaison du déclencheur d’application logique
Une fois que vous avez obtenu le point de terminaison de votre déclencheur, vous pouvez l’enregistrer dans votre système principal et l’appeler par le biais d’un `POST` dans l’URL complète. Vous pouvez inclure des en-têtes et des paramètres de requête supplémentaires, ainsi que du contenu dans le corps.

Si le type de contenu est `application/json`, vous pouvez référencer des propriétés depuis l’intérieur même de la requête. Dans le cas contraire, il est considéré comme une unité binaire unique qui peut être transmise à d’autres API, mais qui ne peut pas faire l’objet d’un référencement interne.

En outre, vous pouvez spécifier un schéma JSON dans la définition. Par conséquent, le concepteur génère des jetons que vous pouvez transférer par étapes. Avec l’exemple suivant, des jetons `title` et `name` sont disponibles dans le Concepteur :

```
{
    "manual": {
        "inputs":{
            "schema": {
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
        }
    }
}
```

## Référencement du contenu de la requête entrante
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

Ce procédé diffère légèrement de la version *2014-12-01-preview* dans laquelle l’accès au corps d’un écouteur HTTP suppose l’utilisation d’une fonction telle que la suivante : `@triggerOutputs().body.Content`.

## Réponse à la requête
Pour certaines requêtes qui démarrent une application logique, vous pouvez répondre à l’appelant avec du contenu. Il existe un nouveau type d’action appelé **response** qui peut être utilisé pour construire le code d’état, le corps et les en-têtes de votre réponse. En l’absence de forme **response**, le point de terminaison de l’application logique répond *immédiatement* avec **202 Accepté** (l’équivalent de l’*envoi automatique de la réponse* dans l’écouteur HTTP).

```
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
    "conditions" : []
}
```

Les réponses ont les propriétés suivantes :

| Propriété | Description |
| -------- | ----------- |
| statusCode | Code d’état HTTP pour répondre à la requête entrante. Ce peut être tout code d’état valide commençant par 2xx, 4xx ou 5xx. Les codes d’état 3xx ne sont pas autorisés. | 
| body | Un objet corps peut être une chaîne, un objet JSON ou même du contenu binaire référencé à partir d’une étape précédente. | 
| headers | Vous pouvez définir n’importe quel nombre d’en-têtes à inclure dans la réponse. | 

Toutes les étapes de l’application logique qui sont requises pour la réponse doivent prendre au maximum *60 secondes*. Si aucune action de réponse n’est atteinte en 60 secondes, la requête entrante expire et reçoit une réponse HTTP **408 Délai d’expiration du client**.

## Configuration avancée du point de terminaison
Les applications logiques prennent automatiquement en charge le point de terminaison d’accès direct et utilisent systématiquement la méthode `POST` pour démarrer l’exécution. En outre, l’application API **Écouteur HTTP** prenait déjà en charge la modification des segments d’URL et de la méthode HTTP. Vous pouviez même renforcer la sécurité ou configurer un domaine personnalisé en l’ajoutant à l’hôte d’application API (l’application web hébergeant l’application API).

Cette fonctionnalité est disponible par le biais de la **Gestion des API**:
* [Modification de la méthode de la requête](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Modification des segments d’URL de la requête](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Configuration de vos domaines de gestion des API dans l’onglet **Configurer** du portail Azure Classic
* Configuration d’une stratégie pour vérifier l’authentification de base (**lien nécessaire**)

## Résumé de la migration à partir de 2014-12-01-preview

| 2014-12-01-preview | 2015-08-01-preview |
|---------------------|--------------------|
| Cliquer sur l’application API **Écouteur HTTP** | Cliquer sur **Déclenchement manuel** (aucune application API requise) |
| Paramètre d’écouteur HTTP « *Envoie une réponse automatiquement* » | Inclusion ou non d’une action **response** dans la définition de flux de travail |
| Configurer l’authentification de base ou OAuth | Par le biais de la gestion des API |
| Configurer la méthode HTTP | Par le biais de la gestion des API |
| Configurer le chemin d’accès relatif | Par le biais de la gestion des API |
| Référencer le corps entrant par le biais de `@triggerOutputs().body.Content` | Référencer par le biais de `@triggerOutputs().body` |
| Action **Envoyer une réponse HTTP** sur l’écouteur HTTP | Cliquer sur **Répondre à la requête HTTP** (aucune application API requise)


[1]: ./media/app-service-logic-http-endpoint/manualtrigger.png

<!---HONumber=AcomDC_0420_2016-->