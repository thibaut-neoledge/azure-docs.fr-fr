---
title: "Recevoir des alertes de journal d’activité sur les notifications de service Azure | Microsoft Docs"
description: "Soyez informé par e-mail, SMS ou Webhook en cas de problème lié au service Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: c8339c08333aaa471b42f6468ca2a59ee4efacf9
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhook pour les alertes de journal d’activité Azure
Dans le cadre de la définition d’un groupe d’actions, vous êtes en mesure de configurer des points de terminaison Webhook pour recevoir des notifications d’alerte de journal d’activité. Les webhooks vous permettent d’acheminer ces notifications vers d’autres systèmes à des fins de post-traitement ou d’exécution d’actions personnalisées. Cet article montre également à quoi ressemble la charge utile d’une requête HTTP POST pour un webhook.

Pour plus d’informations sur la création d’alertes de journal d’activité Azure, [consultez plutôt cette page](monitoring-activity-log-alerts.md).

Pour plus d’informations sur la création de groupes d’actions, [consultez plutôt cette page](monitoring-action-groups.md)

## <a name="authenticating-the-webhook"></a>Authentification du webhook
Le webhook peut procéder à une authentification via une autorisation par jeton : l’URI du webhook est enregistré avec un ID de jeton, par exemple, `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Schéma de la charge utile
La charge utile JSON contenue dans l’opération POST varie en fonction de champ data.context.activityLog.eventSource de la charge utile.

###<a name="common"></a>Courant
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
###<a name="administrative"></a>Administratif
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                }
            }
        },
        "properties": {}
    }
}
```

Pour plus d’informations sur les schémas des alertes de journal d’activité de notification du Service d’intégrité, [cliquez ici](monitoring-service-notifications.md). Pour plus d’informations sur les schémas de toutes les autres alertes de journal d’activité, [cliquez ici](monitoring-overview-activity-logs.md)

| Nom de l’élément | Description |
| --- | --- |
| status |Utilisé pour les alertes de métrique. Toujours défini sur « activated » pour les alertes de journal d’activité. |
| context |Contexte de l’événement. |
| resourceProviderName |Fournisseur de la ressource affectée. |
| conditionType |Toujours défini sur « Event ». |
| name |Nom de la règle d’alerte. |
| id |ID de ressource de l’alerte. |
| description |Description de l’alerte telle que définie lors de la création de l’alerte. |
| subscriptionId |ID d’abonnement Azure. |
| timestamp |Heure à laquelle l’événement a été généré par le service Azure qui a traité la demande. |
| resourceId |ID de ressource de la ressource affectée. |
| resourceGroupName |Nom du groupe de ressources de la ressource affectée. |
| properties |Ensemble de paires `<Key, Value>` (c’est-à-dire, `Dictionary<String, String>`) incluant des détails sur l’événement. |
| event |Élément contenant des métadonnées relatives à l’événement. |
| autorisation |Propriétés de contrôle d’accès en fonction du rôle (RBAC) de l’événement. Il s’agit généralement des propriétés « action », « role » et « scope ». |
| category |Catégorie de l’événement. Les valeurs prises en charge sont : Administrative, Alert, Security, ServiceHealth, Recommendation. |
| caller |Adresse e-mail de l’utilisateur ayant effectué l’opération, la revendication de nom d’utilisateur principal (UPN) ou la revendication de nom de principal du service (SPN) basée sur la disponibilité. Peut être null pour certains appels système. |
| correlationId |Généralement un GUID au format chaîne. Les événements avec correlationId appartiennent à la même action et partagent généralement un correlationId. |
| eventDescription |Description textuelle statique de l’événement. |
| eventDataId |Identificateur unique de l’événement. |
| eventSource |Nom de l’infrastructure ou du service Azure ayant généré l’événement. |
| httpRequest |Il s’agit généralement des requêtes « clientRequestId », « clientIpAddress » et « method » HTTP (PUT, par exemple). |
| minimal |L’une des valeurs suivantes : « Critical », « Error », « Warning », « Informational » et « Verbose ». |
| operationId |Généralement, GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Les propriétés de l’événement. |
| status |Chaîne. État de l’opération. Les valeurs courantes sont : « Started », « In Progress », « Succeeded », « Failed », « Active », « Resolved ». |
| subStatus |Inclut généralement le code d’état HTTP de l’appel REST correspondant. Il peut également inclure d’autres chaînes décrivant un sous-état. Les valeurs courantes sont : OK (Code d’état HTTP : 200), Created (Code d’état HTTP : 201), Accepted (Code d’état HTTP : 202), No Content (Code d’état HTTP : 204), Bad Request (Code d’état HTTP : 400), Not Found (Code d’état HTTP : 404), Conflict (Code d’état HTTP : 409), Internal Server Error (Code d’état HTTP : 500), Service Unavailable (Code d’état HTTP : 503), Gateway Timeout (Code d’état HTTP : 504). |

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur le journal d’activité](monitoring-overview-activity-logs.md)
* [Exécuter des scripts Azure Automation (Runbooks) sur des alertes Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Utiliser une application logique pour envoyer un SMS par le biais de Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
* [Utiliser une application logique pour envoyer un message Slack à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.
* [Utiliser une application logique pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Cet exemple s’applique aux alertes de métrique, mais peut être modifié pour fonctionner avec une alerte de journal d’activité.

