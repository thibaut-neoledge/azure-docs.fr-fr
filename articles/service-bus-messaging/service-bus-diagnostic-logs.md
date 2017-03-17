---
title: "Journaux de diagnostic Azure Service Bus | Microsoft Docs"
description: "Découvrez comment analyser les journaux de diagnostic Service Bus dans Microsoft Azure."
keywords: 
documentationcenter: 
services: service-bus-messaging
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/17/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 90321171586110a3b60c3df5b749003ebbf70ec9
ms.openlocfilehash: 70205b33e9d52e41f5c1a637fee4da192e2a971a
ms.lasthandoff: 02/22/2017


---
# <a name="service-bus-diagnostic-logs"></a>Journaux de diagnostic Service Bus

## <a name="introduction"></a>Introduction
Service Bus expose deux types de journaux : 
* Des [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) qui sont toujours activés et qui fournissent des informations sur les opérations effectuées sur les travaux.
* Des [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) qui sont configurables par l’utilisateur et qui fournissent des informations plus détaillées sur tout ce qui concerne le travail, depuis sa création jusqu’à sa suppression en passant par sa mise à jour et son exécution.

## <a name="how-to-enable-diagnostic-logs"></a>Comment activer les journaux de diagnostic
Les journaux de diagnostic sont **désactivés** par défaut. Pour les activer, procédez comme suit :

Connectez-vous au portail Azure et accédez au panneau du travail de streaming et utilisez le panneau « Journaux de Diagnostic » sous « Surveillance ».

![navigation dans le panneau jusqu’aux journaux de diagnostic](./media/service-bus-diagnostic-logs/image1.png)  

Cliquez ensuite sur le lien « Activer les diagnostics ».

![activer les journaux de diagnostic](./media/service-bus-diagnostic-logs/image2.png)

Dans les diagnostics ouverts, définissez l’état sur « Activé ».

![modifier l’état des journaux de diagnostic](./media/service-bus-diagnostic-logs/image3.png)

Configurez la cible d’archivage souhaitée (compte de stockage, Event Hub, Log Analytics) et sélectionnez les catégories de journaux à collecter (Execution, Authoring). Enregistrez ensuite la nouvelle configuration de diagnostic.

Une fois enregistrée, la configuration entre en vigueur après 10 minutes environ. Au terme de ce délai, les journaux commencent à apparaître dans la cible d’archivage configurée que vous pouvez voir dans le panneau « Journaux de Diagnostics » :

Vous trouverez plus d’informations sur la configuration des diagnostics dans la page des [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-schema"></a>Schéma des journaux de diagnostic

Tous les journaux sont stockés au format JSON, et chaque entrée comprend des champs de chaîne au format présenté ci-après.

### <a name="operation-logs"></a>Journaux des opérations

OperationalLogs capture ce qui se passe lors d’une opération Service Bus (plus particulièrement le type d’opération tel que la création d’une file d’attente, les ressources utilisées et l’état de l’opération).

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
     "EventName": "Create Queue",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Étapes suivantes
* [Introduction à Service Bus](service-bus-messaging-overview.md)
* [Prise en main de Service Bus](service-bus-create-namespace-portal.md)

