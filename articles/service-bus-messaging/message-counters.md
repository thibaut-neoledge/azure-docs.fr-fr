---
title: Nombre de messages Azure Service Bus | Microsoft Docs
description: "Récupérez le nombre de messages Azure Service Bus."
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: sethm
ms.openlocfilehash: f5d2aa551bbe77a66459907cf5cd1313bb907981
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="message-counters"></a>Compteurs de messages

Vous pouvez récupérer le nombre de messages contenus dans les files d’attente et les abonnements à l’aide d’Azure Resource Manager et des API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) Service Bus dans le Kit de développement logiciel (SDK) .NET Framework.

Avec PowerShell, vous pouvez obtenir ce nombre comme suit :

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Détails du nombre de messages

Le fait de connaître le nombre de messages actifs permet de déterminer si une file d’attente génère un backlog dont le traitement nécessite davantage de ressources que celles déployées actuellement. Les détails de compteur disponibles dans la classe [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) sont les suivants :

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount) : messages de la file d’attente ou de l’abonnement qui présentent l’état actif et sont prêts à être remis.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount) : messages figurant dans la file d’attente de lettres mortes.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount) : messages à l’état planifié.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount) : messages dont le transfert vers une autre file d’attente ou rubrique a échoué et qui ont été déplacés vers la file d’attente de lettres mortes de transfert.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount) : messages en attente de transfert vers une autre file d’attente ou rubrique.

Si une application a besoin de mettre à l’échelle les ressources en fonction de la longueur de la file d’attente, elle doit le faire très progressivement. L’acquisition des compteurs de messages est une opération onéreuse dans le répartiteur de messages, dont l’exécution fréquente compromet directement les performances des entités.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les articles suivants :

* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)