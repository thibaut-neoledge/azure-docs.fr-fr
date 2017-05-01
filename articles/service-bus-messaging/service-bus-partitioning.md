---
title: "Créer des rubriques et des files d’attente Azure Service Bus partitionnées | Microsoft Docs"
description: "Décrit comment partitionner des files d’attente et des rubriques Service Bus à l’aide de plusieurs courtiers de messages."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a0c7d5a2-4876-42cb-8344-a1fc988746e7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: sethm;hillaryc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 946f3ac069db436828427e575be5a14efac9dda9
ms.lasthandoff: 04/03/2017


---
# <a name="partitioned-queues-and-topics"></a>Files d’attente et rubriques partitionnées
Azure Service Bus utilise plusieurs courtiers de messages pour traiter les messages et plusieurs banques de messagerie pour stocker les messages. Une file d’attente ou une rubrique classique est gérée par un seul courtier de messages et stockée dans une seule banque de messagerie. Service Bus permet également le *partitionnement* des files d’attente ou des rubriques entre plusieurs courtiers de messages et banques de messagerie. Cela signifie que le débit global d’une file d’attente ou d’une rubrique partitionnée n’est plus limité par les performances d’un seul courtier de messages ou d’une seule banque de messagerie. En outre, la panne temporaire d’une banque de messagerie ne rend pas une rubrique ou une file d’attente partitionnée indisponible. Les rubriques et les files d’attente partitionnées peuvent contenir toutes les fonctionnalités avancées de Service Bus, comme la prise en charge des transactions et des sessions.

Pour plus d’informations sur les éléments internes de Service Bus, consultez l’article [Architecture de Service Bus][Service Bus architecture].

## <a name="how-it-works"></a>Fonctionnement
Chaque file d’attente ou rubrique partitionnée est constituée de plusieurs fragments. Chaque fragment est stocké dans une banque de messagerie différente et géré par un courtier de messages différent. Lorsqu’un message est envoyé à une file d’attente ou une rubrique partitionnée, Service Bus affecte le message à l’un des fragments. La sélection est effectuée au hasard par Service Bus ou à l’aide d’une clé de partition que l’expéditeur peut spécifier.

Lorsqu’un client souhaite recevoir un message à partir d’une file d’attente partitionnée ou à partir d’un abonnement à une rubrique partitionnée, Service Bus interroge tous les fragments des messages, puis retourne le premier message qui est obtenu à partir de l’une des banques de messagerie au destinataire. Service Bus place les autres messages en cache pour les retourner quand il reçoit d'autres requêtes de réception. Un client destinataire n’est pas conscient de ce partitionnement ; le comportement côté client d’une file d’attente ou d’une rubrique partitionnée (par exemple, lecture, exécution, report, rebut, préchargement) est identique à celui d’une entité ordinaire.

Il n’existe aucun coût supplémentaire lors de l’envoi d’un message à, ou lors de la réception d’un message depuis, une file d’attente ou une rubrique partitionnée.

## <a name="enable-partitioning"></a>Activation du partitionnement
Pour utiliser des rubriques et des files d’attente partitionnées avec Azure Service Bus, utilisez le Kit de développement logiciel (SDK) Microsoft Azure version 2.2 ou version ultérieure, ou spécifiez `api-version=2013-10` dans vos requêtes HTTP.

Vous pouvez créer des files d’attente et des rubriques Service Bus avec des tailles de 1, 2, 3, 4 ou 5 Go (la valeur par défaut est 1 Go). Si le partitionnement est activé, Service Bus crée 16 partitions pour chaque Go que vous spécifiez. Par conséquent, si vous créez une file d’attente de 5 Go, avec 16 partitions, la taille maximale de la file d’attente est (5 \* 16) = 80 Go. Vous pouvez voir la taille maximale de votre file d’attente ou rubrique partitionnée en examinant son entrée sur le [portail Azure][Azure portal].

Il existe plusieurs façons de créer une file d’attente ou une rubrique partitionnée. Lorsque vous créez la file d’attente ou la rubrique à partir de votre application, vous pouvez activer le partitionnement de la file d’attente ou de la rubrique en définissant respectivement la propriété [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] ou [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] sur **true**. Ces propriétés doivent être définies au moment où la file d’attente ou la rubrique est créée. Il n’est pas possible de modifier ces propriétés sur une file d’attente ou une rubrique existante. Par exemple :

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Vous pouvez également créer une file d’attente ou une rubrique partitionnée dans Visual Studio ou sur le [portail Azure][Azure portal]. Lorsque vous créez une file d’attente ou une rubrique dans le portail, définissez l’option **Activer le partitionnement** dans le volet **Paramètres généraux** de la fenêtre **Paramètres** de la file d’attente ou de la rubrique sur **true**. Dans Visual Studio, cochez la case **Activer le partitionnement** dans la boîte de dialogue **Nouvelle file d’attente** ou **Nouvelle rubrique**.

## <a name="use-of-partition-keys"></a>Utilisation de clés de partition
Lorsqu’un message est placé dans une file d’attente ou une rubrique partitionnée, Service Bus vérifie la présence d’une clé de partition. S'il en trouve une, il sélectionne le fragment basé sur cette clé. S'il ne trouve pas de clé de partition, il sélectionne le fragment basé sur un algorithme interne.

### <a name="using-a-partition-key"></a>Utilisation d’une clé de partition
Certains scénarios, tels que les sessions ou les transactions, nécessitent que les messages soient stockés dans un fragment spécifique. Tous ces scénarios requièrent l’utilisation d’une clé de partition. Tous les messages qui utilisent la même clé de partition sont affectés au même fragment. Si le fragment est momentanément indisponible, Service Bus renvoie une erreur.

Selon le scénario, différentes propriétés de messages sont utilisées comme clé de partition :

**SessionId** : si un message a la propriété [BrokeredMessage.SessionId][BrokeredMessage.SessionId] définie, alors Service Bus utilise cette propriété comme clé de partition. De cette façon, tous les messages qui appartiennent à la même session sont gérés par le même courtier de messages. Cela permet à Service Bus de garantir l'ordre des messages, ainsi que la cohérence des états de session.

**PartitionKey** : si un message a la propriété [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] définie mais pas la propriété [BrokeredMessage.SessionId][BrokeredMessage.SessionId], alors Service Bus utilise la propriété [PartitionKey][PartitionKey] comme clé de partition. Si le message a les deux propriétés [SessionId][SessionId] et [PartitionKey][PartitionKey] définies, alors elles doivent être identiques. Si la propriété [PartitionKey][PartitionKey] est définie sur une valeur différente de celle de la propriété [SessionId][SessionId], Service Bus retourne une exception d’opération non valide. La propriété [PartitionKey][PartitionKey] doit être utilisée si un expéditeur envoie des messages transactionnels non liés à une session. La clé de partition garantit que tous les messages qui sont envoyés dans une transaction sont gérés par le même courtier de messages.

**MessageId** : si la file d’attente ou la rubrique a la propriété [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] définie sur **true** et que les propriétés [BrokeredMessage.SessionId][BrokeredMessage.SessionId] ou [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] ne sont pas définies, la propriété [BrokeredMessage.MessageId][BrokeredMessage.MessageId] sert de clé de partition. (Notez que les bibliothèques Microsoft .NET et AMQP attribuent automatiquement un ID de message si l'application émettrice ne le fait pas.) Dans ce cas, toutes les copies du même message sont gérées par le même courtier de messages. Cela permet à Service Bus de détecter et d’éliminer les messages en double. Si la propriété [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] n’est pas définie sur **true**, Service Bus ne considère pas la propriété [MessageId][MessageId] comme clé de partition.

### <a name="not-using-a-partition-key"></a>Non utilisation d'une clé de partition
En l’absence d’une clé de partition, Service Bus distribue les messages en tourniquet à tous les fragments de la file d’attente ou la rubrique partitionnée. Si le fragment choisi n'est pas disponible, Service Bus affecte le message à un fragment différent. De cette façon, l'opération d'envoi réussit malgré l'indisponibilité temporaire d'une banque de messagerie. Cependant, vous obtiendrez pas le classement garanti qui est fourni par une clé de partition.

Pour une discussion plus approfondie sur le compromis entre la disponibilité (pas de clé de partition) et la cohérence (en utilisant une clé de partition), consultez [cet article](../event-hubs/event-hubs-availability-and-consistency.md). Ces informations s’appliquent également aux entités Service Bus partitionnées et aux partitions Event Hubs.

Pour donner à Service Bus assez de temps pour placer le message en file d’attente dans un fragment différent, la valeur [MessagingFactorySettings.OperationTimeout][MessagingFactorySettings.OperationTimeout] spécifiée par le client qui envoie le message doit être supérieure à 15 secondes. Il est recommandé de définir la propriété [OperationTimeout][OperationTimeout] sur la valeur par défaut de 60 secondes.

Notez qu'une clé de partition « épingle » un message à un fragment spécifique. Si la banque de messagerie qui contient ce fragment est indisponible, Service Bus renvoie une erreur. En l'absence d'une clé de partition, Service Bus peut choisir un fragment différent et l'opération réussit. Par conséquent, il est recommandé de ne pas fournir de clé de partition, sauf si elle est requise.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Rubriques avancées : utiliser des transactions avec des entités partitionnées
Les messages envoyés dans le cadre d’une transaction doivent spécifier une clé de partition. Il peut s’agir de l’une des propriétés suivantes : [BrokeredMessage.SessionId][BrokeredMessage.SessionId], [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] ou [BrokeredMessage.MessageId][BrokeredMessage.MessageId]. Tous les messages envoyés dans le cadre d'une même transaction doivent spécifier la même clé de partition. Si vous essayez d’envoyer un message sans une clé de partition dans une transaction, Service Bus renvoie une exception d’opération non valide. Si vous essayez d’envoyer plusieurs messages dans la même transaction avec des clés de partition différentes, Service Bus renvoie une exception d’opération non valide. Par exemple :

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Si les propriétés qui servent de clé de partition sont définies, Service Bus épingle le message à un fragment spécifique. Ce comportement se produit qu'une transaction soit utilisée ou non. Il est recommandé de ne pas spécifier de clé de partition si elle n'est pas nécessaire.

## <a name="using-sessions-with-partitioned-entities"></a>Utilisation de sessions avec les entités partitionnées
Pour envoyer un message transactionnel à une file d’attente ou une rubrique utilisant une session, le message doit contenir la propriété [BrokeredMessage.SessionId][BrokeredMessage.SessionId] définie. Si la propriété [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] est également spécifiée, elle doit être identique à la propriété [SessionId][SessionId]. Si ces propriétés diffèrent, Service Bus renvoie une exception d’opération non valide.

Contrairement aux files d’attente ou rubriques standard (non partitionnées), il n’est pas possible d’utiliser une transaction unique pour envoyer plusieurs messages à différentes sessions. En cas de tentative, Service Bus renvoie une exception d’opération non valide. Par exemple :

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Transfert automatique des messages avec des entités partitionnées
Service Bus prend en charge le transfert automatique des messages à partir de, vers ou entre les entités partitionnées. Pour activer le transfert automatique des messages, définissez la propriété [QueueDescription.ForwardTo][QueueDescription.ForwardTo] de la file d’attente ou de l’abonnement source. Si le message spécifie une clé de partition ([SessionId][SessionId], [PartitionKey][PartitionKey] ou [MessageId][MessageId]), cette clé de partition est utilisée pour l’entité de destination.

## <a name="considerations-and-guidelines"></a>Considérations et recommandations
* **Fonctionnalités de cohérence élevée** : si une entité utilise des fonctionnalités telles que des sessions, la détection des doublons ou un contrôle explicite de la clé de partitionnement, les opérations de messagerie sont toujours acheminées vers des fragments spécifiques. Si le trafic d’un des fragments est élevé ou si le magasin sous-jacent est défectueux, ces opérations échouent et la disponibilité s’en trouve réduite. En général, la cohérence reste plus importante que le nombre d’entités non partitionnées. Seul un sous-ensemble de trafic rencontre des problèmes, et non le trafic tout en entier. Pour plus d’informations, consultez cette [discussion sur la disponibilité et la cohérence](../event-hubs/event-hubs-availability-and-consistency.md).
* **Gestion** : des opérations telles que la création, la mise à jour et la suppression doivent être exécutées sur tous les fragments de l’entité. Si un fragment est défectueux, cela peut entraîner l’échec de ces opérations. Pour l’opération Get, des informations telles que le nombre de messages doivent être agrégées à partir de tous les fragments. Si un fragment est défectueux, l’état de disponibilité de l’entité est signalé comme étant limité.
* **Scénarios de messages à faible volume** : dans ce cas, notamment lorsque vous utilisez le protocole HTTP, vous devez peut-être effectuer plusieurs opérations de réception afin d’obtenir tous les messages. Pour les demandes de réception, le serveur frontal effectue une opération de réception sur tous les fragments et met en cache toutes les réponses reçues. Une demande de réception ultérieure sur la même connexion profiterait de cette mise en cache et on assisterait à une réduction des latences liées à la réception. Toutefois, si vous disposez de plusieurs connexions ou si vous utilisez HTTP, une nouvelle connexion est établie pour chaque demande. Par conséquent, l’accès au même nœud n’est pas garanti. Si tous les messages existants sont verrouillés et mis en cache sur un autre serveur frontal, l’opération de réception renvoie la valeur **null**. Les messages peuvent arriver à expiration et vous pouvez les recevoir à nouveau. La persistance du protocole HTTP est recommandée.
* **Parcourir/Afficher l’aperçu des messages** : [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) ne retourne pas toujours le nombre de messages spécifié dans la propriété [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_MessageCount).
 Il existe deux raisons courantes à cela. L’une des raisons est que la taille agrégée de la collection de messages dépasse la taille maximale de 256 Ko. Une autre raison est que si la [propriété EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning) de la file d’attente ou de la rubrique est définie sur **true**, il se peut qu’une partition ne dispose pas de suffisamment de messages pour terminer le nombre de messages demandé. En règle générale, si une application veut recevoir un certain nombre de messages, elle doit appeler [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) à plusieurs reprises jusqu’à obtenir le nombre de messages voulu à moins qu’il n’y ait plus de messages pour lesquels afficher un aperçu. Pour plus d’informations, y compris pour obtenir des exemples de code, consultez [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) ou [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_PeekBatch_System_Int32_).

## <a name="latest-added-features"></a>Dernières fonctionnalités ajoutées
* L’ajout ou la suppression de règles est désormais pris en charge avec les entités partitionnées. À la différence des entités non partitionnée, ces opérations ne sont pas prises en charge dans le cadre des transactions. 
* AMQP est désormais pris en charge pour l’envoi et la réception de messages vers et à partir d’une entité partitionnée.
* AMQP est désormais pris en charge pour les opérations suivantes : [Envoi par lots](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_BrokeredMessage__), [Réception par lots](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ReceiveBatch_System_Int32_), [Réception par numéro de séquence](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Receive_System_Int64_), [Affichage d’aperçu](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Peek), [Verrouillage du renouvellement](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_RenewMessageLock_System_Guid_), [Planification des messages](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ScheduleMessageAsync_Microsoft_ServiceBus_Messaging_BrokeredMessage_System_DateTimeOffset_), [Annulation des messages planifiés](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_CancelScheduledMessageAsync_System_Int64_), [Ajout de règles](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [Suppression de règles](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [Verrouillage de renouvellement de session](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_RenewLock), [Définition de l’état d’une session](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_), [Obtention de l’état d’une session](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_GetState) et [Énumération des sessions](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessionsAsync).

## <a name="partitioned-entities-limitations"></a>Limites des entités partitionnées
Actuellement, Service Bus impose les limites suivantes aux files d’attente et rubriques partitionnées :

* Les rubriques et files d’attente partitionnées ne prennent pas en charge l’envoi de messages appartenant à des sessions différentes dans une transaction unique.
* Service Bus permet actuellement de disposer jusqu’à 100 rubriques ou files d’attente par espace de noms. Chaque file d’attente ou rubrique partitionnée est comptabilisée dans le quota de 10 000 entités par espace de noms (non applicable au niveau Premium).

## <a name="next-steps"></a>Étapes suivantes
Consultez la discussion sur la [prise en charge par AMQP 1.0 des rubriques et files d’attente partitionnées de Service Bus][AMQP 1.0 support for Service Bus partitioned queues and topics] pour en savoir plus sur le partitionnement des entités de messagerie. 

[Service Bus architecture]: service-bus-architecture.md
[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription#Microsoft_ServiceBus_Messaging_TopicDescription_EnablePartitioning
[BrokeredMessage.SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[BrokeredMessage.PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[QueueDescription.RequiresDuplicateDetection]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessagingFactorySettings.OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md

