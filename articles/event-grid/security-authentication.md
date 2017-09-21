---
title: "Sécurité et authentification Azure Event Grid"
description: "Détaille le service Azure Event Grid et ses concepts."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/14/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 9bc82e628df5e380db84e22e1f5fd25f75929fdc
ms.contentlocale: fr-fr
ms.lasthandoff: 09/13/2017

---

# <a name="event-grid-security-and-authentication"></a>Sécurité et authentification Azure Event Grid 

Azure Event Grid dispose de trois types d’authentification :

* Abonnements à des événements
* Publication d’événement
* Remise d’événement WebHook

## <a name="webhook-event-delivery"></a>Remise d’événement WebHook

Webhook constitue l’un des nombreux moyens de réception d’événements en temps réel depuis Azure Event Grid.

Chaque fois qu’un nouvel événement est prêt à être remis, Event Grid envoie une requête HTTP à votre Webhook avec l’événement dans le corps.

Lorsque vous inscrivez votre point de terminaison WebHook avec Event Grid, le WebHook vous envoie une requête POST avec un code de validation simple pour prouver que vous êtes le propriétaire du point de terminaison. Votre application doit répondre en renvoyant le code de validation. Event Grid ne remet aucun événement aux points de terminaison WebHook qui n’ont pas été validés.
 
### <a name="validation-details"></a>Détails de validation :

* Lors de la création/mise à jour de l’abonnement d’événement, Event Grid publie un événement nommé « SubscriptionValidationEvent » dans le point de terminaison cible.
* L’événement contient une valeur d’en-tête « Event-Type: Validation ».
* Le corps de l’événement dispose du même schéma que les autres événements Event Grid.
* Les données d’événement incluent une propriété « ValidationCode » avec une chaîne générée de façon aléatoire, par exemple « ValidationCode : acb13... ».

Un exemple de SubscriptionValidationEvent est donné ci-dessous.
```json
[{
  "Id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "Topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "Subject": "",
  "Data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "EventType": "Microsoft.EventGrid/SubscriptionValidationEvent",
  "EventTime": "2017-08-06T22:09:30.740323Z"
}]
```

Afin de prouver que vous êtes propriétaire du point de terminaison, retournez le code de validation, par exemple : « validation_response: acb13... », dont un exemple est affiché ci-dessous.

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Afin de prouver que vous êtes propriétaire du point de terminaison, renvoyez le code de validation, par exemple : « ValidationResponse : acb13... ».

Enfin, il est important de noter qu’Azure Event Grid ne prend en charge que les points de terminaison HTTPS webhook.
## <a name="event-subscription"></a>Abonnement à un événement

Pour vous abonner à un événement, vous devez disposer de l’autorisation **Microsoft.EventGrid/EventSubscriptions/Write** sur la ressource nécessaire. Il vous faut cette autorisation, car vous rédigez un nouvel abonnement dans la portée de la ressource. La ressource nécessaire diffère si vous vous abonnez à une rubrique du système ou à une rubrique personnalisée. Les deux types sont décrits dans cette section.

### <a name="system-topics-azure-service-publishers"></a>Rubriques du système (éditeurs du service Azure)

Dans les rubriques du système, il vous faut l’autorisation de rédiger un nouvel abonnement à un événement dans la portée de la ressource qui publie l’événement. La ressource est au format suivant : `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Par exemple, pour vous abonner à un événement sur un compte de stockage nommé **myacct**, il vous faut l’autorisation Microsoft.EventGrid/EventSubscriptions/Write sur :`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Rubriques personnalisées

Dans les rubriques personnalisées, il vous faut l’autorisation de rédiger un nouvel abonnement à un événement dans la portée de la rubrique Event Grid. La ressource est au format suivant : `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Par exemple, pour vous abonner à une rubrique personnalisée nommée **mytopic**, il vous faut l’autorisation Microsoft.EventGrid/EventSubscriptions/Write sur :`/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>Publication d’une rubrique

Les rubriques utilisent une Signature d’accès partagé (SAP) ou une authentification par clé. Nous vous recommandons la SAP, mais l’authentification par clé propose une programmation simple et est compatible avec de nombreux éditeurs de webhook existants. 

Vous incluez la valeur d’authentification dans l’en-tête HTTP. Pour une SAP, utilisez **aeg-sas-token** en valeur de l’en-tête. Pour une authentification par clé, utilisez **aeg-sas-key** en valeur de l’en-tête.

### <a name="key-authentication"></a>Authentification par clé

L’authentification par clé est la plus simple. Utilisez le format : `aeg-sas-key: <your key>`

Par exemple, vous transmettez une clé avec : 

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Jetons SAS

Les jetons SAP pour Event Grid incluent la ressource, un délai d’expiration et une signature. Le jeton SAP est au format suivant : `r={resource}&e={expiration}&s={signature}`.

La ressource représente le chemin d’accès à la rubrique à laquelle vous envoyez des événements. Par exemple, un chemin d’accès de ressource valide est :`https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Vous générez la signature à partir d’une clé.

Par exemple, une valeur **aeg-sas-token** valide est :

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
``` 

L’exemple suivant crée un jeton SAP utilisable avec Event Grid :

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    string encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString());

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Contrôle d’accès à la gestion

Azure Event Grid vous permet de contrôler le niveau d’accès offert aux utilisateurs, leur permettant d’effectuer différentes opérations de gestion telles que répertorier et créer des abonnements aux événements et générer des clés. Pour cela, Event Grid utilise la vérification d’accès par rôle (RBAC).

### <a name="operation-types"></a>Types d’opération

Azure Event Grid prend en charge les actions suivantes :

* Microsoft.EventGrid/*/read 
* Microsoft.EventGrid/*/write 
* Microsoft.EventGrid/*/delete 
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action 
* Microsoft.EventGrid/topics/listKeys/action 
* Microsoft.EventGrid/topics/regenerateKey/action

Les trois dernières opérations retournent des informations potentiellement confidentielles qui sont filtrées lors d’opérations de lecture normales. Nous vous recommandons de restreindre l’accès à ces opérations. Il est possible de créer des rôles personnalisés à l’aide [d’Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md), de [l’interface CLI Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md) et de [l’API REST](../active-directory/role-based-access-control-manage-access-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Application de la vérification d’accès par rôle (RBAC)

Pour appliquer la vérification d’accès par rôle, pour différents utilisateurs, procédez comme suit :

#### <a name="create-a-custom-role-definition-file-json"></a>Créer un fichier de définition de rôle personnalisé (.json)

Voici des exemples de définitions de rôle dans Event Grid permettant aux utilisateurs d’effectuer différentes actions.

**EventGridReadOnlyRole.json** : pour autoriser uniquement les opérations en lecture seule.
```json
{ 
  "Name": "Event grid read only role", 
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856", 
  "IsCustom": true, 
  "Description": "Event grid read only role", 
  "Actions": [ 
    "Microsoft.EventGrid/*/read" 
  ], 
  "NotActions": [ 
  ], 
  "AssignableScopes": [ 
    "/subscriptions/<Subscription Id>" 
  ] 
}
```

**EventGridNoDeleteListKeysRole.json** : pour autoriser des actions de publication limitées, et interdire les actions de suppression.
```json
{ 
  "Name": "Event grid No Delete Listkeys role", 
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174", 
  "IsCustom": true, 
  "Description": "Event grid No Delete Listkeys role", 
  "Actions": [     
    "Microsoft.EventGrid/*/write", 
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action" 
    "Microsoft.EventGrid/topics/listkeys/action", 
    "Microsoft.EventGrid/topics/regenerateKey/action" 
  ], 
  "NotActions": [ 
    "Microsoft.EventGrid/*/delete" 
  ], 
  "AssignableScopes": [ 
    "/subscriptions/<Subscription id>" 
  ] 
}
```

**EventGridContributorRole.json**: pour autoriser toutes les actions dans Event Grid.  
```json
{ 
  "Name": "Event grid contributor role", 
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514", 
  "IsCustom": true, 
  "Description": "Event grid contributor role", 
  "Actions": [ 
    "Microsoft.EventGrid/*/write", 
    "Microsoft.EventGrid/*/delete", 
    "Microsoft.EventGrid/topics/listkeys/action", 
    "Microsoft.EventGrid/topics/regenerateKey/action", 
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action" 
  ], 
  "NotActions": [], 
  "AssignableScopes": [ 
    "/subscriptions/d48566a8-2428-4a6c-8347-9675d09fb851" 
  ] 
} 
```

#### <a name="install-and-login-to-azure-cli"></a>Installer et se connecter à Azure CLI

* Azure CLI version 0.8.8 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez [Installer et configurer Azure CLI](../cli-install-nodejs.md).
* Azure Resource Manager dans l’interface de ligne de commande Azure. Pour plus d’informations, consultez [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md) .

#### <a name="create-a-custom-role"></a>Créer un rôle personnalisé

Pour créer un rôle personnalisé, utilisez :

    azure role create --inputfile <file path>

#### <a name="assign-the-role-to-a-user"></a>Affecter le rôle à un utilisateur


    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>


## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez [Event Grid](overview.md)

