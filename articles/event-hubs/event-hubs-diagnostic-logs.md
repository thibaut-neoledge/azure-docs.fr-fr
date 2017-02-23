---
title: Journaux de diagnostic Azure Event Hubs | Microsoft Docs
description: "Découvrez comment analyser les journaux de diagnostic d’Event Hubs dans Microsoft Azure."
keywords: 
documentationcenter: 
services: event-hubs
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 4d7d0e1f5ffb395bf91bbdac1ab4b9ec3f9dee1c
ms.openlocfilehash: cb8c7930ee423543c91366de64220ee55609a4cf


---
# <a name="event-hub-diagnostic-logs"></a>Journaux de diagnostic Event Hub

## <a name="introduction"></a>Introduction
Event Hubs expose deux types de journaux : 
* Des [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) qui sont toujours activés et qui fournissent des informations sur les opérations effectuées sur les travaux.
* Des [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) qui sont configurables par l’utilisateur et qui fournissent des informations plus détaillées sur tout ce qui concerne le travail, depuis sa création jusqu’à sa suppression en passant par sa mise à jour et son exécution.

## <a name="how-to-enable-diagnostic-logs"></a>Comment activer les journaux de diagnostic
Les journaux de diagnostic sont **désactivés** par défaut. Pour les activer, procédez comme suit :

Connectez-vous au portail Azure et accédez au panneau du travail de streaming et utilisez le panneau « Journaux de Diagnostic » sous « Surveillance ».

![navigation dans le panneau jusqu’aux journaux de diagnostic](./media/event-hubs-diagnostic-logs/image1.png)  

Cliquez ensuite sur le lien « Activer les diagnostics ».

![activer les journaux de diagnostic](./media/event-hubs-diagnostic-logs/image2.png)

Dans les diagnostics ouverts, définissez l’état sur « Activé ».

![modifier l’état des journaux de diagnostic](./media/event-hubs-diagnostic-logs/image3.png)

Configurez la cible d’archivage souhaitée (compte de stockage, Event Hub, Log Analytics) et sélectionnez les catégories de journaux à collecter (Execution, Authoring). Enregistrez ensuite la nouvelle configuration de diagnostic.

Une fois enregistrée, la configuration entre en vigueur après 10 minutes environ. Au terme de ce délai, les journaux commencent à apparaître dans la cible d’archivage configurée que vous pouvez voir dans le panneau « Journaux de Diagnostics » :

Vous trouverez plus d’informations sur la configuration des diagnostics dans la page des [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-categories"></a>Catégories de journaux de diagnostic
Nous capturons actuellement deux catégories de journaux de diagnostic :

* **ArchivalLogs :** capture les journaux liés à Event Hubs Archive (spécifiquement liés aux erreurs d’Archive).
* **OperationalLogs :** capture ce qui se passe lors d’une opération dans Event Hubs (plus particulièrement le type d’opération tel que la création d’un Event Hub, les ressources utilisées et l’état de l’opération).

## <a name="diagnostic-logs-schema"></a>Schéma des journaux de diagnostic
Tous les journaux sont stockés au format JSON et chaque entrée comprend les champs de chaîne au format présenté ci-après :


### <a name="archive-error-schema"></a>Schéma d’erreur Archive
Nom | Description
------- | -------
TaskName | La description de la tâche ayant échoué
ActivityId | ID interne fourni à des fins de suivi
trackingId | ID interne fourni à des fins de suivi
resourceId | ID de ressource ARM
eventHub | Nom complet de l’Event Hub (nom d’espace de noms inclus)
partitionId | La partition en cours de rédaction dans l’Event Hub
archiveStep | ArchiveFlushWriter
startTime | Heure de début de la défaillance
failures | Nombre de fois où la défaillance a eu lieu
durationInSeconds | Durée de la défaillance
Message | Message d’erreur
category | ArchiveLogs

#### <a name="example-archive-log"></a>Exemple de journal Archive

```json
{
     "TaskName": "EventHubArchiveUserError",
     "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
     "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
     "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
     "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
     "partitionId": "1",
     "archiveStep": "ArchiveFlushWriter",
     "startTime": "9/22/2016 5:11:21 AM",
     "failures": 3,
     "durationInSeconds": 360,
     "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
     "category": "ArchiveLogs"
}
```

### <a name="operation-logs-schema"></a>Schéma des journaux des opérations
Nom | Description
------- | -------
ActivityId | ID interne fourni à des fins de suivi
EventName | Nom d’opération           
resourceId | ID de ressource ARM
SubscriptionId | ID d’abonnement
EventTimeString | Durée de l’opération
EventProperties | Propriétés de l’opération
État | État de l’opération
Appelant | Appelant de l’opération (portail ou client de gestion)
category | OperationalLogs

#### <a name="example-operation-log"></a>Exemple de journal des opérations

```json
Example: 
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create EventHub",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Présentation des API Event Hubs](event-hubs-api-overview.md)
* [Prise en main des hubs d’événements](event-hubs-csharp-ephcs-getstarted.md)


<!--HONumber=Feb17_HO1-->


