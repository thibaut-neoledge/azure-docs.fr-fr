<properties
	pageTitle="Présentation du journal d’activité Azure | Microsoft Azure"
	description="Découvrez le journal d’activités Azure et comment vous pouvez l’utiliser pour comprendre les événements qui se produisent dans votre abonnement Azure."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="johnkem"/>

# Présentation du journal d’activité Azure
Le **journal d’activité Azure** est un journal qui fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement. Le journal d’activité était précédemment appelé « journal d’audit » ou « journal des opérations », car il indique les événements de plan de contrôle pour vos abonnements. À l’aide du journal d’activité, vous pouvez obtenir diverses informations (« quoi, qui et quand ») pour toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur les ressources dans votre abonnement. En outre, vous pouvez connaître l’état de l’opération et d’autres propriétés pertinentes. Le journal d’activité n’inclut pas les opérations (GET) de lecture.

Le journal d’activité est différent des [journaux de diagnostic](./monitoring-overview-of-diagnostic-logs.md), qui correspondent à tous les journaux émis par une ressource. Ces journaux fournissent des informations sur l’opération de cette ressource, plutôt que sur les opérations sur cette ressource.

Vous pouvez extraire des événements de votre journal d’activité à l’aide du portail Azure, de l’interface de ligne de commande, des applets de commande PowerShell et de l’API REST Insights.

## Ce que vous pouvez faire avec le journal d’activité
Voici ce que vous pouvez faire avec le journal d’activité :

- Interrogez-le et affichez-le dans le **portail Azure**.
- Interrogez-le via l’API REST, l’applet de commande PowerShell ou l’interface de ligne de commande.
- [Créez une alerte via e-mail ou webhook qui déclenche un événement de journal d’activité.](./insights-auditlog-to-webhook-email.md)
- [Enregistrez-le dans un **compte de stockage** pour l’archivage ou l’inspection manuelle](./monitoring-archive-activity-log.md). Vous pouvez spécifier la durée de rétention (en jours) à l’aide des **profils de journal**.
- Analysez-le dans PowerBI à l’aide du [**pack de contenu PowerBI**](https://powerbi.microsoft.com/fr-FR/documentation/powerbi-content-pack-azure-audit-logs/).
- [Diffusez-le en continu vers un **Event Hub**](./monitoring-stream-activity-logs-event-hubs.md) pour qu’un service tiers ou une solution d’analyse personnalisée (p. ex. PowerBI) l’ingère.

## Export du journal d’activité avec les profils de journal
Un **profil de journal** contrôle comment votre journal d’activité est exporté. À l’aide d’un profil de journal, vous pouvez configurer :

- L’emplacement où le journal d’activité doit être envoyé (compte de stockage ou Event Hubs).
- Les catégories d’événements (p. ex. Write, Delete, Action) qui doivent être envoyées.
- Les régions (emplacements) qui doivent être exportées.
- La durée de rétention du journal d’activité dans un compte de stockage ; une durée de rétention de zéro jour signifie que les journaux sont conservés indéfiniment. La valeur peut également être n’importe quel nombre de jours, compris entre 1 et 2147483647. Si des stratégies de rétention sont définies, mais que le stockage des journaux dans un compte de stockage est désactivé (p. ex. si seules les options Event Hubs ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.

Ces paramètres peuvent être configurés via l’option d’export dans le panneau Journal d’activité dans le portail, ou par programme [à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx), des applets de commande PowerShell ou de l’interface de ligne de commande. Un abonnement ne peut avoir qu’un seul profil de journal.

### Configuration des profils de journal à l’aide du portail Azure
Vous pouvez diffuser en continu le journal d’activité vers un Event Hub ou le stocker dans un compte de stockage à l’aide de l’option d’export dans le portail Azure.

1. Accédez au panneau **Journal d’activité** à l’aide du menu sur le côté gauche du portail.

    ![Accès au journal d’activité dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Cliquez sur le bouton **Exporter** en haut du panneau.

    ![Bouton Exporter dans le portail](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. Dans le panneau qui s’affiche, vous pouvez sélectionner les régions pour lesquelles vous souhaitez exporter les événements, le compte de stockage dans lequel vous souhaitez enregistrer les événements (ainsi que le nombre de jours pendant lesquels vous souhaitez conserver ces événements dans le stockage ; si vous indiquez 0, les journaux seront conservés indéfiniment) et l’espace de noms Service Bus dans lequel vous souhaitez qu’un Event Hub soit créé pour diffuser en continu ces événements.

    ![Panneau Exporter le journal d’activité](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Cliquez sur **Enregistrer** pour enregistrer ces paramètres. Les paramètres sont immédiatement appliqués à votre abonnement.

### Configuration des profils de journal à l’aide des applets de commande Azure PowerShell
#### Obtention du profil de journal existant
```
Get-AzureRmLogProfile
```

#### Ajout d’un profil de journal
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propriété | Requis | Description |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name | Oui | Nom de votre profil de journal. |
| StorageAccountId | Non | ID de ressource du compte de stockage dans lequel le journal d’activité doit être enregistré. |
| serviceBusRuleId | Non | ID de règle Service Bus pour l’espace de noms Service Bus dans lequel vous souhaitez que des concentrateurs d’événements soient créés. Ce sera une chaîne au format suivant : `{service bus resource ID}/authorizationrules/{key name}`. |
| Emplacements | Oui | Liste séparée par des virgules des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. |
| RetentionInDays | Oui | Nombre de jours pendant lesquels les événements doivent être conservés, compris entre 1 et 2147483647. Une valeur de zéro signifie que les journaux seront stockés pour une durée indéfinie (pour toujours). |
| Catégories | Non | Liste séparée par des virgules des catégories d’événements qui doivent être collectées. Les valeurs possibles sont Write, Delete et Action. |

#### Supprimer un profil de journal
```
Remove-AzureRmLogProfile -name my_log_profile
```

### Configuration des profils de journal à l’aide de l’interface de ligne de commande interplateforme Azure
#### Obtention du profil de journal existant
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
La propriété `name` doit être le nom de votre profil de journal.

#### Ajout d’un profil de journal
``` 
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propriété | Requis | Description |
|------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name | Oui | Nom de votre profil de journal. |
| storageId | Non | ID de ressource du compte de stockage dans lequel le journal d’activité doit être enregistré. |
| serviceBusRuleId | Non | ID de règle Service Bus pour l’espace de noms Service Bus dans lequel vous souhaitez que des concentrateurs d’événements soient créés. Ce sera une chaîne au format suivant : `{service bus resource ID}/authorizationrules/{key name}`. |
| emplacements | Oui | Liste séparée par des virgules des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. |
| retentionInDays | Oui | Nombre de jours pendant lesquels les événements doivent être conservés, compris entre 1 et 2147483647. Une valeur de zéro signifie que les journaux seront stockés pour une durée indéfinie (pour toujours). |
| categories | Non | Liste séparée par des virgules des catégories d’événements qui doivent être collectées. Les valeurs possibles sont Write, Delete et Action. |

#### Supprimer un profil de journal
```
azure insights logprofile delete --name my_log_profile
```

## Schéma d’événement
Chaque événement dans le journal d’activité a un objet blob JSON comme celui-ci :

```
{
  "value": [ {
    "authorization": {
      "action": "microsoft.support/supporttickets/write",
      "role": "Subscription Admin",
      "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
    },
    "caller": "admin@contoso.com",
    "channels": "Operation",
    "claims": {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
      "iat": "1421876371",
      "nbf": "1421876371",
      "exp": "1421880271",
      "ver": "1.0",
      "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
      "puid": "20030000801A118C",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
      "name": "John Smith",
      "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
      "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.microsoft.com/claims/authnclassreference": "1"
    },
    "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "description": "",
    "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
    "eventName": {
      "value": "EndRequest",
      "localizedValue": "End request"
    },
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
    },
    "httpRequest": {
      "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
      "clientIpAddress": "192.168.35.115",
      "method": "PUT"
    },
    "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
    "level": "Informational",
    "resourceGroupName": "MSSupportGroup",
    "resourceProviderName": {
      "value": "microsoft.support",
      "localizedValue": "microsoft.support"
    },
    "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
    "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
    "operationName": {
      "value": "microsoft.support/supporttickets/write",
      "localizedValue": "microsoft.support/supporttickets/write"
    },
    "properties": {
      "statusCode": "Created"
    },
    "status": {
      "value": "Succeeded",
      "localizedValue": "Succeeded"
    },
    "subStatus": {
      "value": "Created",
      "localizedValue": "Created (HTTP Status Code: 201)"
    },
    "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
    "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
    "subscriptionId": "s1"
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| Nom de l’élément | Description |
|----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| autorisation | Objet blob des propriétés RBAC de l’événement. Inclut généralement les propriétés « action », « role » et « scope ». |
| caller | Adresse e-mail de l’utilisateur qui a effectué l’opération, la revendication UPN ou la revendication SPN basée sur la disponibilité. |
| channels | L’une des valeurs suivantes : « Admin », « Operation ». |
| correlationId | Généralement un GUID au format chaîne. Les événements qui partagent un correlationId appartiennent à la même action uber. |
| description | Description textuelle statique d’un événement. |
| eventDataId | Identificateur unique d’un événement. |
| eventSource | Nom de l’infrastructure ou du service Azure qui a généré cet événement. |
| httpRequest | Objet blob décrivant la requête Http. Inclut généralement clientRequestId, clientIpAddress et la méthode (méthode HTTP PUT, par exemple). |
| level | Niveau de l’événement. Une des valeurs suivantes : Critical, Error, Warning, Informational et Verbose |
| resourceGroupName | Nom du groupe de ressources de la ressource affectée. |
| resourceProviderName | Nom du fournisseur de ressources de la ressource affectée. |
| resourceUri | ID de ressource de la ressource affectée. |
| operationId | Un GUID partagé par les événements correspondant à une opération unique. |
| operationName | Nom de l’opération. |
| properties | Jeu de paires `<Key, Value>` (p. ex. Dictionary) décrivant les détails de l’événement. |
| status | Chaîne décrivant l’état de l’opération. Certaines valeurs courantes sont : Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Il s’agit généralement du code d’état HHTP de l’appel REST correspondant, mais également d’autres chaînes décrivant un sous-état, comme ces valeurs courantes : OK (Code d’état HTTP : 200), Created (Code d’état HTTP : 201), Accepted (Code d’état HTTP : 202), No content (Code d’état HTTP : 204), Bad Request (Code d’état HTTP : 400), Not found (Code d’état HTTP : 404), Conflict (Code d’état HTTP : 409), Internal Server Error (Code d’état HTTP : 500), Service Unavailable (Code d’état HTTP : 503), Gateway Timeout (Code d’état HTTP : 504). |
| eventTimestamp | Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| submissionTimestamp | Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId | ID d’abonnement Azure. |
| nextLink | Jeton de continuation pour extraire le jeu de résultats suivant lorsqu’ils sont divisés en plusieurs réponses. C’est généralement le cas s’il existe plus de 200 enregistrements. |

## Étapes suivantes
- [En savoir plus sur le journal d’activité (autrefois appelé journal d’audit)](../resource-group-audit.md)
- [Stream the Azure Activity Log to Event Hubs (Diffuser en continu le journal d’activités Azure vers Event Hubs)](./monitoring-stream-activity-logs-event-hubs.md)

<!---HONumber=AcomDC_0907_2016-->