---
title: "Journaux de diagnostic Azure Service Bus | Microsoft Docs"
description: "Découvrez comment configurer les journaux de diagnostic pour Service Bus dans Azure."
keywords: 
documentationcenter: .net
services: service-bus-messaging
author: banisadr
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2017
ms.author: babanisa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: b8ed786fe0c049d9be7ba1ca1cb6adef1950b8e9
ms.lasthandoff: 03/24/2017


---
# <a name="service-bus-diagnostic-logs"></a>Journaux de diagnostic Service Bus

Vous pouvez afficher deux types de journaux pour Azure Service Bus :
* **[Journaux d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)**. Ces journaux contiennent des informations sur les opérations effectuées sur un travail. Les journaux sont toujours activés.
* **[Journaux de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)**. Vous pouvez configurer les journaux de diagnostic pour obtenir des informations plus détaillés sur tous les événements associés à un travail. Les journaux de diagnostic couvrent les activités qui se déroulent entre la création du travail et sa suppression, notamment les mises à jour et les activités durant l’exécution du travail.

## <a name="turn-on-diagnostic-logs"></a>Activer les journaux de diagnostic
Les journaux de diagnostic sont désactivés par défaut. Pour activer les journaux de diagnostic, procédez comme suit :

1.    Dans le [portail Azure](https://portal.azure.com), accédez au panneau de diffusion en continu du travail.

2.    Sous **Analyse**, accédez au panneau **Journaux de diagnostic**.

    ![navigation dans le panneau jusqu’aux journaux de diagnostic](./media/service-bus-diagnostic-logs/image1.png)  

3.    Cliquez sur **Activer les diagnostics**.

    ![activer les journaux de diagnostic](./media/service-bus-diagnostic-logs/image2.png)

4.    Pour l’**état**, cliquez sur **ACTIVÉ**.

    ![modifier l’état des journaux de diagnostic](./media/service-bus-diagnostic-logs/image3.png)

5.    Définissez la cible d’archivage de votre choix, par exemple un compte de stockage, un concentrateur d’événements ou Azure Log Analytics.

6.    Sélectionnez les catégories de journaux à collecter, par exemple **Exécution** ou **Création**.

7.    Enregistrez les nouveaux paramètres de diagnostic.

Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux apparaissent dans la cible d’archivage configurée, dans le panneau **Journaux de diagnostic**.

Pour plus d’informations sur la configuration des diagnostics, consultez la [vue d’ensemble des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

## <a name="diagnostic-logs-schema"></a>Schéma des journaux de diagnostic

Tous les journaux sont stockés au format JSON (JavaScript Object Notation). Chaque entrée comporte des champs de type chaîne qui utilisent le format décrit dans l’exemple suivant.

## <a name="operation-logs-example"></a>Exemple de journal des opérations

Les journaux de la catégorie **OperationalLogs** capturent ce qui se passe durant l’opération Service Bus. Plus précisément, ces journaux capturent le type d’opération, notamment la création de la file d’attente, les ressources utilisées et l’état de l’opération.

Les chaînes JSON du journal des opérations incluent les éléments répertoriés dans le tableau suivant :

Nom | Description
------- | -------
ActivityId | ID interne, utilisé à des fins de suivi
EventName | Nom d’opération             
resourceId | ID de ressource Azure Resource Manager
SubscriptionId | Identifiant d’abonnement
EventTimeString | Durée de l’opération
EventProperties | Propriétés de l’opération
État | État de l’opération
Appelant | Appelant de l’opération (portail Azure ou client de gestion)
category | OperationalLogs

Voici un exemple de chaîne JSON du journal des opérations :

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
* [Bien démarrer avec Service Bus](service-bus-dotnet-get-started-with-queues.md)

