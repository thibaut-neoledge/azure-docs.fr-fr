<properties 
   pageTitle="Espaces de noms associés Service Bus | Microsoft Azure"
   description="Détails sur l'implémentation de l'espace de noms associé et coût"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/18/2015"
   ms.author="sethm" />

# Détails sur l'implémentation de l'espace de noms associé et implications en termes de coût

La méthode [PairNamespaceAsync][], utilisant une instance [SendAvailabilityPairedNamespaceOptions][], effectue des tâches visibles à votre place. Étant donné qu'il existe des considérations relatives aux coûts lors de l'utilisation de la fonctionnalité, il est utile de comprendre ces tâches afin que vous vous attendiez au comportement lorsque celui-ci se produit. L'API engage le comportement automatique suivant à votre place :

-   Création de files d'attente de travaux en souffrance.
-   Création d'un objet [MessageSender][] qui communique avec les files d'attente ou rubriques.
-   Lorsqu'une entité de messagerie devient indisponible, envoie des messages ping à l'entité dans le but de détecter lorsque cette entité est à nouveau disponible.
-   Ou, crée un ensemble de « pompes de messages » qui déplacent les messages depuis les files d'attente de travaux en souffrance vers les files d'attente principales.
-   Coordonne la clôture/défaillance des instances [MessagingFactory][] principale et secondaire.

À un niveau élevé, la fonctionnalité fonctionne comme suit : lorsque l'entité principale est intègre, aucun changement de comportement ne se produit. Lorsque la durée [FailoverInterval][] est écoulée et que l'entité principale ne constate aucun envoi réussi après une valeur [MessagingException][] ou [TimeoutException][] non temporaire, le comportement suivant se produit :

1.  Les opérations d'envoi à l'entité principale sont désactivées et le système interroge l'entité principale jusqu'à ce que les tests ping puissent être remis avec succès.

2.  Une file d'attente de travaux en souffrance aléatoire est sélectionnée.

3.  Les objets [BrokeredMessage][] sont acheminés vers la file d'attente de travaux en souffrance choisie.

1.  En cas d'échec d'une opération d'envoi vers la file d'attente de travaux en souffrance choisie, cette file d'attente est extraite de la rotation et une nouvelle file d'attente est sélectionnée. Tous les expéditeurs de l'instance [MessagingFactory][] sont mis au courant de l'échec.

Les figures suivantes décrivent la séquence. Tout d'abord, l'expéditeur envoie des messages.

![Espaces de noms associés][0]

En cas d'échec de l'envoi à la file d'attente principale, l'expéditeur commence à envoyer des messages à une file d'attente de travaux en souffrance choisie de façon aléatoire. Simultanément, il démarre une tâche ping.

![Espaces de noms associés][1]

À ce stade, les messages sont toujours dans la file d'attente secondaire et n'ont pas été remis à la file d'attente principale. Une fois que la file d'attente principale redevient intègre, au moins un processus doit exécuter le siphon. Le siphon remet les messages de toutes les files d'attente de travaux en souffrance aux entités de destination appropriées (files d'attente et rubriques).

![Espaces de noms associés][2]

Le reste de cette rubrique décrit les détails spécifiques du fonctionnement de ces éléments.

## Création de files d'attente de travaux en souffrance

L'objet [SendAvailabilityPairedNamespaceOptions][] transmis à la méthode [PairNamespaceAsync][] indique le nombre de files d'attente de travaux en souffrance que vous voulez utiliser. Chaque file d'attente de travaux en souffrance est créée avec les propriétés suivantes explicitement définies (toutes les autres valeurs sont définies sur la valeur [QueueDescription][] par défaut) :

| Path | [primary namespace]/x-servicebus-transfer/[index] où [index] est une valeur dans [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes | 5120 |
| MaxDeliveryCount | int.MaxValue |
| DefaultMessageTimeToLive | TimeSpan.MaxValue |
| AutoDeleteOnIdle | TimeSpan.MaxValue |
| LockDuration | 1 minute |
| EnableDeadLetteringOnMessageExpiration | true |
| EnableBatchedOperations | true |

Par exemple, la première file d'attente de travaux en souffrance créée pour l'espace de noms **contoso** est nommée `contoso/x-servicebus-transfer/0`.

Lorsque vous créez les files d'attente, le code vérifie d'abord si cette file d'attente existe. Dans le cas contraire, la file d'attente est créée. Le code ne nettoie pas les files d'attente de travaux en souffrance « supplémentaires ». Plus précisément, si l'application avec l'espace de noms principal **contoso** demande cinq files d'attente de travaux en souffrance, mais qu'une file d'attente de travaux en souffrance avec le chemin d'accès `contoso/x-servicebus-transfer/7` existe, cette file d'attente de travaux en souffrance supplémentaire est toujours présente, mais n'est pas utilisée. Le système autorise explicitement l'existence de files d'attente de travaux en souffrance qui ne seraient pas utilisées. En tant que propriétaire de l'espace de noms, vous êtes chargé de nettoyer les files d'attente de travaux en souffrance inutilisées/indésirables. La raison est que Service Bus ne peut pas connaître les objectifs de toutes les files d'attente dans votre espace de noms. En outre, si une file d'attente existe avec le nom donné, mais qu'elle ne se conforme PAS à la valeur [QueueDescription][], alors les raisons de la modification du comportement par défaut vous appartiennent. Aucune garantie n'est donnée pour les modifications apportées aux files d'attente de travaux en souffrance par votre code. Veillez à tester vos modifications de manière approfondie.

## MessageSender personnalisé

Lors de l'envoi, tous les messages passent par un objet [MessageSender][] interne qui se comporte normalement lorsque tout fonctionne et redirige vers les files d'attente de travaux en souffrance en cas de dysfonctionnement. Lors de la réception d'une défaillance non temporaire, une minuterie démarre. Après une période [TimeSpan][] comprenant la valeur de la propriété [FailoverInterval][] au cours de laquelle aucun message n'est envoyé, le basculement est activé. À ce stade, les événements suivants se produisent pour chaque entité :

- Une tâche ping exécute chaque [PingPrimaryInterval][] pour vérifier si l'entité est disponible. Lorsque cette tâche réussit, tout le code client qui utilise l'entité commence immédiatement à envoyer de nouveaux messages à l'espace de noms principal.

- Les futures requêtes consistant à envoyer à cette même entité à partir de n'importe quel autre expéditeur provoquent la modification du [BrokeredMessage][] envoyé, qui doit alors se trouver dans la file d'attente. La modification supprime certaines propriétés de l'objet [BrokeredMessage][] pour les stocker ailleurs. Les propriétés suivantes sont effacées et ajoutées sous un nouvel alias, ce qui permet à Service Bus et au Kit de développement logiciel de traiter les messages de manière uniforme :

| Nom de l'ancienne propriété | Nom de la nouvelle propriété |
|-------------------------|-------------------|
| SessionId | x-ms-sessionid |
| TimeToLive | x-ms-timetolive |
| ScheduledEnqueueTimeUtc | x-ms-path |

Le chemin de destination d'origine est également stocké dans le message en tant que propriété nommée x-ms-path. Cette conception permet à des messages de plusieurs entités de coexister dans une seule file d'attente de travaux en souffrance. Les propriétés sont traduites par le siphon.

L'objet [MessageSender][] personnalisé peut rencontrer des problèmes lorsque les messages approchent de la limite de 256 Ko et que le basculement est activé. L'objet [MessageSender][] personnalisé stocke les messages pour toutes les files d'attente et rubriques dans les files d'attente de travaux en souffrance. Cet objet combine des messages provenant de plusieurs sources principales avec les files d'attente de travaux en souffrance. Pour gérer l'équilibrage de charge entre plusieurs clients qui ne se connaissent pas mutuellement, le Kit de développement logiciel choisit de façon aléatoire une file d'attente de travaux en souffrance pour chaque [QueueClient][] ou [TopicClient][] que vous créez dans le code.

## Tests ping

Un message ping est un [BrokeredMessage][] vide avec sa propriété [ContentType][] définie sur application/vnd.ms-servicebus-ping et une valeur [TimeToLive][] de 1 seconde. Cette commande ping a une caractéristique particulière dans Service Bus : le serveur ne remet jamais une commande ping lorsqu'un appelant demande un [BrokeredMessage][]. Par conséquent, vous n'avez pas besoin de savoir comment recevoir et ignorer ces messages. Chaque entité (file d'attente ou rubrique unique) par instance [MessagingFactory][] par client sera interrogée si elle est considérée comme non disponible. Par défaut, cela se produit une fois par minute. Les messages ping sont considérés comme des messages Service Bus normaux et peuvent entraîner des frais de bande passante et de messagerie. Dès que les clients détectent que le système est disponible, les messages s'arrêtent.

## Le siphon

Au moins un programme exécutable de l'application doit exécuter activement le siphon. Le siphon effectue une longue interrogation de réception qui dure 15 minutes. Lorsque toutes les entités sont disponibles et que vous avez 10 files d'attente de travaux en souffrance, l'application qui héberge le siphon appelle l'opération de réception 40 fois par heure, 960 fois par jour et 28 800 fois en 30 jours. Lorsque le siphon déplace activement des messages à partir de la file d'attente de travaux en souffrance vers la file d'attente principale, chaque message est soumis aux frais suivants (des frais standard pour la taille des messages et la bande passante s'appliquent à toutes les étapes) :

1.  Envoyer à la file d'attente de travaux en souffrance.

2.  Recevoir depuis la file d'attente de travaux en souffrance.

3.  Envoyer à la file d'attente principale.

4.  Recevoir depuis la file d'attente principale.

## Comportement de fermeture/erreur

Dans une application qui héberge le siphon, si l'instance [MessagingFactory][] principale ou secondaire connaît une défaillance ou est fermée sans que son partenaire connaisse une défaillance/soit fermé également et que le siphon détecte cet état, le siphon réagit. Si l'autre instance [MessagingFactory][] n'est pas fermée dans les 5 secondes, le siphon place l'instance [MessagingFactory][] toujours ouverte en état de défaillance.

## Étapes suivantes

Consultez [Modèles de messagerie asynchrone et haute disponibilité] pour une discussion détaillée sur la messagerie asynchrone de Service Bus.

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Modèles de messagerie asynchrone et haute disponibilité]: service-bus-async-messaging.md

<!---HONumber=Oct15_HO3-->