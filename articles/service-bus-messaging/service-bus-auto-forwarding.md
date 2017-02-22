---
title: "Transfert automatique d’entités de messagerie de Service Bus | Microsoft Docs"
description: "Comment chaîner une file d’attente ou un abonnement à une autre file d’attente ou rubrique."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: cbbd416a065b3284e85957cc024955d11524d3da


---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Chaînage des entités Service Bus avec transfert automatique
La fonctionnalité de *transfert automatique* vous permet de chaîner une file d’attente ou un abonnement à une autre file d’attente ou rubrique qui fait partie du même espace de noms. Lorsque le transfert automatique est activé, Service Bus supprime automatiquement les messages placés dans la première file d’attente ou le premier abonnement (source) pour les placer dans la deuxième file d’attente ou rubrique (destination). Notez qu'il est toujours possible d'envoyer un message à l'entité de destination directement. Notez également qu’il n’est pas possible de chaîner une sous-file d’attente, comme une file d’attente de rebut, à une autre file d’attente ou rubrique.

## <a name="using-auto-forwarding"></a>Utilisation du transfert automatique
Vous pouvez activer le transfert automatique en définissant les propriétés [QueueDescription.ForwardTo][QueueDescription.ForwardTo] ou [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] sur les objets [QueueDescription][QueueDescription] ou [SubscriptionDescription][SubscriptionDescription] pour la source, comme dans l’exemple suivant.

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

L'entité de destination doit exister au moment de la création de l'entité source. Si l'entité de destination n'existe pas, Service Bus renvoie une exception lorsqu'il lui est demandé de créer l'entité source.

Vous pouvez utiliser le transfert automatique pour faire évoluer une rubrique particulière. Service Bus limite le [nombre d’abonnements à une rubrique donnée](service-bus-quotas.md) à 2 000. Vous pouvez créer des abonnements supplémentaires en créant des rubriques de second niveau. Même si vous n’êtes pas lié par la limitation de Service Bus sur le nombre d’abonnements, l’ajout d’un deuxième niveau de rubriques peut améliorer le débit global de votre rubrique.

![Scénario de transfert automatique][0]

Vous pouvez également utiliser le transfert automatique pour découpler les expéditeurs de messages des récepteurs. Par exemple, considérez un système ERP qui se compose de trois modules : Traitement des commandes, Gestion des stocks et Gestion des relations client. Chacun de ces modules génère des messages qui sont placés en file d’attente dans une rubrique correspondante. Alice et Bob sont des représentants commerciaux qui s'intéressent à tous les messages liés à leurs clients. Pour recevoir ces messages, Alice et Bob créent chacun une file d’attente personnelle et un abonnement sur chacune des rubriques ERP qui transfèrent automatiquement tous les messages à leur file d’attente.

![Scénario de transfert automatique][1]

Si Alice part en vacances, sa file d’attente personnelle, et non la rubrique ERP, se remplit. Dans ce scénario, étant donné qu’un représentant commercial n’a pas reçu les messages, aucun des rubriques ERP n’atteint jamais son quota.

## <a name="auto-forwarding-considerations"></a>Considérations sur le transfert automatique
Si l’entité de destination a accumulé de nombreux messages et dépasse le quota, ou l’entité de destination est désactivée, l’entité source ajoute les messages à sa [file d’attente de rebut](service-bus-dead-letter-queues.md) jusqu’à ce qu’il y ait de l’espace dans la destination (ou que l’entité soit réactivée). Ces messages continuent de résider dans la file d'attente de rebut, donc vous devez explicitement les recevoir et les traiter à partir de la file d'attente de rebut.

Lors du chaînage de rubriques individuelles pour obtenir une rubrique composite avec de nombreux abonnements, nous vous recommandons d’avoir un nombre modéré d’abonnements à la rubrique de premier niveau et beaucoup d’abonnements aux rubriques de second niveau. Par exemple, une rubrique de premier niveau avec 20 abonnements, chacun étant chaîné à une rubrique de second niveau possédant 200 abonnements, permet d’obtenir un débit plus élevé qu’une rubrique de premier niveau possédant 200 abonnements, chacun étant chaîné à une rubrique de second niveau possédant 20 abonnements.

Service Bus facture une opération pour chaque message transféré. Par exemple, l’envoi d’un message à une rubrique possédant 20 abonnements, chacun d’eux étant configuré pour transférer automatiquement les messages vers une autre file d’attente ou rubrique, est facturé en tant que 21 opérations si tous les abonnements de premier niveau reçoivent une copie du message.

Pour créer un abonnement qui est chaîné à une autre file d’attente ou rubrique, le créateur de l’abonnement doit disposer des autorisations de **gestion** de l’entité source et l’entité de destination. L’envoi de messages à la rubrique source ne nécessite que des autorisations **d’envoi** sur la rubrique source.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le transfert automatique, consultez les rubriques de référence suivantes :

* [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Pour en savoir plus sur les améliorations des performances de Service Bus, consultez [Entités de messagerie partitionnées][Partitioned messaging entities].

[QueueDescription.ForwardTo]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md



<!--HONumber=Jan17_HO2-->


