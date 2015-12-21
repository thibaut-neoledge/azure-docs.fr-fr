<properties 
   pageTitle="Messagerie asynchrone Service Bus | Microsoft Azure"
   description="Description de la messagerie asynchrone répartie Service Bus."
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

# Modèles de messagerie asynchrone et haute disponibilité

Une messagerie asynchrone peut être mise en œuvre de différentes façons. Avec des files d’attente, rubriques et abonnements (appelés collectivement les entités de messagerie) Azure Service Bus prend en charge des opérations asynchrones en utilisant un mécanisme de stockage et de transfert. En cas de fonctionnement normal (synchrone), des messages sont envoyés à des files d’attente et des rubriques et vous recevez des messages de la part de files d’attente et d’abonnements. Les applications que vous rédigez dépendent de la disponibilité permanente de ces entités. Lorsque, suite à certaines circonstances, l’état de l’entité change, vous devez disposer d’une entité à fonctionnalités réduites pouvant satisfaire la plupart des besoins.

Les applications utilisent en général des modèles de messagerie asynchrones autorisant un certain nombre de scénarios de communication. Vous pouvez créer des applications dans lesquelles les clients peuvent envoyer des messages aux services, même si le service n’est pas en cours d’exécution. Pour les applications soumises à des pics de communication, une file d’attente peut permettre de répartir la charge en fournissant un emplacement pour mettre les communications en mémoire tampon. Enfin, vous avez accès à un équilibreur de charge simple mais efficace pour distribuer les messages sur plusieurs ordinateurs.

Pour maintenir la disponibilité de toutes ces entités, vous devez trouver des moyens de faire apparaître ces entités comme indisponibles pour un système de messagerie durable. En règle générale, l’entité devient indisponible pour les applications que nous écrivons pour différentes raisons :

1.  Impossible d’envoyer des messages

2.  Impossible de recevoir des messages

3.  Impossible d’administrer (créer, récupérer, mettre à jour ou supprimer) des entités.

4.  Impossible de contacter le service.

Pour chacune de ces erreurs, différents modes d’échec existent et permettent à une application de continuer de fonctionner à un niveau de fonctionnalités limité. Par exemple, un système qui peut envoyer des messages mais pas de les recevoir peut encore recevoir des commandes des clients, mais ne peut pas traiter ces commandes. Cette rubrique aborde les problèmes qui peuvent se présenter et explique comment y remédier. Service Bus présente un certain nombre de mesures d’atténuation à votre disposition, et cette rubrique traite également des règles régissant l’utilisation de ces actions.

## Fiabilité de Service Bus

Il existe différentes méthodes pour gérer les problèmes de messagerie et d’entités et certaines instructions régissent l’utilisation appropriée de ces actions de correction. Pour comprendre ces instructions, vous devez d’abord connaître les défaillances susceptibles de se produire dans Service Bus. En raison de la conception des systèmes Azure, tous ces problèmes sont en général éphémères. À un niveau élevé, les différentes causes d’indisponibilité sont les suivantes :

-   Limitation d’un système externe dont Service Bus dépend. La limitation se produit à la suite d’interactions avec les ressources de stockage et de calcul.

-   Problème d’un système dont Service Bus dépend. Par exemple, une partie donnée du stockage peut rencontrer des problèmes.

-   Défaillance de Service Bus dans un sous-système unique Dans ce cas, un nœud de calcul peut passer à l’état incohérent et redémarrer seul, ce qui fait que toutes les entités desservies doivent équilibrer leur charge sur d’autres nœuds. Pendant un bref laps de temps, le traitement des messages peut se trouver ralenti.

-   Échec de Service Bus dans un centre de données Azure. Il s’agit d’une « défaillance irrémédiable » classique, et le système reste inaccessible pendant plusieurs minutes, voire quelques heures.

> [AZURE.NOTE]Le terme **stockage** peut désigner le stockage Azure et SQL Azure.

Service Bus propose un certain nombre de mesures d’atténuation des problèmes mentionnés ci-dessus. Les sections suivantes décrivent chaque problème et les actions correctives correspondantes.

### Limitation

Avec Service Bus, la limitation permet la gestion coopérative de la vitesse des messages. Chaque nœud Service Bus héberge de nombreuses entités. Chacune de ces entités effectue des demandes sur le système en termes de processeur, de mémoire, de stockage et autres. Lorsqu’une de ces facettes détecte une utilisation dépassant les seuils définis, Service Bus peut refuser une demande donnée. L’appelant reçoit alors un [ServerBusyException][] et refait une tentative après 10 secondes.

Pour remédier au problème, le code doit lire l’erreur et stopper toute nouvelle tentative du message pendant au moins 10 secondes. Dans la mesure où l’erreur peut se produire sur différents éléments de l’application client, chaque élément doit exécuter la nouvelle tentative de manière indépendante. Le code peut réduire la probabilité de limitation en activant le partitionnement sur une file d’attente ou une rubrique.

### Problème sur une dépendance Azure

D’autres composants dans Azure peuvent parfois rencontrer des problèmes de service. Par exemple, lorsqu’un système Service Bus est en cours de mise à niveau, ce système peut provisoirement présenter des fonctionnalités réduites. Pour résoudre ces types de problèmes, Service Bus procède régulièrement à des analyses et met en œuvre les solutions de correction. Des effets secondaires de ces mesures de correction peuvent apparaître. Par exemple, pour gérer les problèmes temporaires liés au stockage, Service Bus fait appel à un système qui permet aux opérations d’envoi de message de continuer de fonctionner manière cohérente. En raison de la nature de cette mesure de correction, un message envoyé peut prendre jusqu’à 15 minutes pour apparaître dans la file d’attente ou l’abonnement affecté et être prêt pour une opération de réception. En règle générale, la plupart des entités ne rencontrent pas ce problème. Toutefois, étant donné le nombre d’entités présentes dans Service Bus au sein d’Azure, cette opération est parfois nécessaire pour un petit sous-ensemble de clients de Service Bus.

### Défaillance Service Bus dans un sous-système unique

Quelle que soit l’application, les circonstances peuvent générer une défaillance chez un composant interne de Service Bus. Lorsque Service Bus détecte cela, il rassemble les données de l’application pour vous aider à diagnostiquer ce qui est arrivé. Une fois les données collectées, l’application est redémarrée afin de revenir à un état cohérent. Ce processus est assez rapidement et peut faire en sorte qu’une entité semble indisponible pendant quelques minutes, bien que les interruptions de service par défaut soient beaucoup plus courtes.

Dans ce cas, l’application cliente génère une exception [System.TimeoutException][] ou [MessagingException][]. Le kit de développement logiciel Service Bus .NET contient un correctif pour ce problème sous la forme d’un programme de nouvelle tentative cliente automatisée. Une fois la période de nouvelle tentative épuisée, si le message n’est pas remis, vous pouvez entamer des recherches à l’aide d’autres fonctionnalités, par exemple, les espaces de noms associés. Les espaces de noms associés génèrent d’autres notifications, qui sont abordées plus loin dans ce document.

### Panne de Service Bus dans un centre de données Azure.

La cause la plus probable de défaillance dans un centre de données Azure est l’échec du déploiement d’une mise à niveau de Service Bus ou d’un système dépendant. Au fur et à mesure que la plateforme évolue, l’éventualité de ce type de panne diminue. Une défaillance de centre de données peut également se produire pour les raisons suivantes :

-   Panne électrique (l’alimentation électrique et les générateurs de courant sont interrompus).
-   Connectivité (interruption d’Internet entre vos clients et Azure).

Dans les deux cas, une catastrophe naturelle ou une erreur humaine a provoqué le problème. Pour contourner ce problème et vous assurer que vous pouvez toujours envoyer des messages, vous pouvez utiliser des espaces de noms associés pour acheminer les messages vers un second emplacement pendant la restauration de l’emplacement principal. Pour plus d’informations, consultez la section [Meilleures pratiques d’isolation des applications Service Bus des pannes et des sinistres][]

## Espaces de noms associés

La fonctionnalité d’espaces de noms associés prend en charge les scénarios dans lesquels une entité Service Bus ou un déploiement dans un centre de données devient indisponible. Bien qu’un tel événement soit rare, les systèmes distribués toujours doivent être préparés aux pires scénarios. En règle générale, cet événement se produit lorsque certains éléments dont dépend Bus Services rencontrent un problème de courte durée. Pour maintenir la disponibilité des applications pendant une panne, les utilisateurs de Service Bus peuvent utiliser deux espaces de noms différents, de préférence dans des centres de données distincts, pour héberger leurs entités de messagerie. Le reste de cette section utilise la terminologie suivante :

-   Espace de noms principal : espace de noms avec lequel l’application interagit pour envoyer et recevoir des opérations.

-   Espace de noms secondaire : espace de noms servant de sauvegarde de l’espace de noms principal. La logique d’application n’interagit pas avec cet espace de noms.

-   Intervalle de basculement : durée d’une panne permettant d’accepter les pannes normales avant que l’application bascule de l’espace de noms principal vers l’espace de noms secondaire.

Les espaces de noms associés prennent en charge la disponibilité des fonctions d’envoi. La disponibilité des possibilités d’envoi se concentre sur le maintien de la possibilité d’envoyer des messages. Pour utiliser la disponibilité d’envoi, votre application doit répondre aux exigences suivantes :

1.  Les messages sont reçus uniquement à partir de l’espace de noms principal.

2.  Les messages envoyés à une file d’attente/rubrique donnée peuvent arriver dans le désordre.

3.  Si votre application utilise des sessions : les messages d’une session peuvent arriver dans le désordre. Ce comportement diffère du comportement normal des sessions. Cela signifie que votre application utilise des sessions pour regrouper les messages de façon logique. L’état de la session est conservé uniquement au niveau de l’espace de noms principal.

4.  Les messages au sein d’une session peuvent arriver dans le désordre. Ce comportement diffère du comportement normal des sessions. Cela signifie que votre application utilise des sessions pour regrouper les messages de façon logique.

5.  L’état de la session est conservé uniquement au niveau de l’espace de noms principal.

6.  La file d’attente principale peut se mettre en ligne et commencer à accepter des messages avant que la file d’attente secondaire ait placé tous les messages dans la file d’attente principale.

Les sections suivantes décrivent les API, la façon dont les API sont implémentées et présente des exemples de code qui utilisent la fonctionnalité. Notez que cette fonctionnalité est associée à des coûts.

### API MessagingFactory.PairNamespaceAsync

La fonctionnalité d’espaces de noms associés introduit la nouvelle méthode suivante sur la classe [Microsoft.ServiceBus.Messaging.MessagingFactory][] classe :

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Lorsque la tâche est terminée, l’association de l’espace de noms se termine elle aussi et elle est prête à fonctionner pour chaque entité [MessageReceiver][], [QueueClient][] ou [TopicClient][] créée avec [MessagingFactory][]. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] est la classe de base correspondant aux différents types d’associations disponibles avec [MessagingFactory][]. Actuellement, la seule classe dérivée est la classe nommée [SendAvailabilityPairedNamespaceOptions][], qui met en œuvre les exigences associées à la disponibilité des envois. [SendAvailabilityPairedNamespaceOptions][] possède un ensemble de constructeurs qui s’appuient les uns sur les autres. En examinant le constructeur comportant le plus grand nombre de paramètres, vous pouvez comprendre le comportement des autres constructeurs.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Ces paramètres ont les significations suivantes :

-   *secondaryNamespaceManager* : instance [NamespaceManager][] initialisée pour l’espace l’espace de noms secondaire que la méthode [PairNamespaceAsync][] peut utiliser pour configurer l’espace de noms secondaire. Le gestionnaire sera utilisé pour obtenir la liste des files d’attente dans l’espace de noms et s’assurer que les files d’attente backlog requises existent. Si ce n’est pas le cas, ces files d’attente sont créées. [NamespaceManager][] doit avoir la possibilité de créer un jeton avec la revendication **gérer** .

-   *messagingFactory* : instance [MessagingFactory][] de l’espace de noms secondaire. [MessagingFactory][] est utilisé pour procéder aux envois, et si [EnableSyphon][] est défini sur **true**, pour recevoir des messages de la part des files d’attente.

-   *backlogQueueCount* : nombre de files d’attente à créer. La valeur doit être au moins à 1. Lorsque vous envoyez des messages à la file d’attente, l’une d’entre elles est sélectionnée de manière aléatoire. Si vous définissez la valeur sur 1, une seule file d’attente peut être utilisée. Lorsque cela se produit et que la seule file d’attente génère des erreurs, le client ne peut pas faire de tentative avec une autre file d’attente et risque de ne pas envoyer votre message. Nous vous recommandons de définir cette valeur sur une valeur supérieure et la valeur par défaut est de 10. Vous pouvez lui attribuer une valeur supérieure ou inférieure selon la quantité de données que votre application envoie quotidiennement. Chaque file d’attente peut contenir jusqu’à 5 Go de messages.

-   *failoverInterval* : durée pendant laquelle vous allez accepter les défaillances sur l’espace de noms principal avant de basculer chaque entité sur l’espace de noms secondaire. Les basculements se produisent sur la base entité par entité. Les entités dans un espace de noms unique résident fréquemment sur différents nœuds au sein de Service Bus. La défaillance d’une entité n’implique pas nécessairement la défaillance d’une autre. Vous pouvez définir cette valeur sur [System.TimeSpan.Zero][] pour basculer sur le second espace de noms tout de suite après votre première défaillance non temporaire. Les défaillances qui déclenchent le minuteur de basculement sont les exceptions [MessagingException][] dans lesquelles la propriété [IsTransient][] a la valeur false ou [System.TimeoutException][]. D’autres exceptions, telles que [UnauthorizedAccessException][] ne provoquent pas de basculement, car elles signalent que le client est configuré de façon incorrecte. Une exception [ServerBusyException][] n’entraîne pas de basculement, car le modèle correct consiste à attendre 10 secondes, puis à renvoyer le message.

-   *enableSyphon* : indique que cette association particulière doit siphonner les messages de l’espace de noms secondaire et les placer sur l’espace de noms principal. En général, les applications qui envoient des messages doivent définir cette valeur sur **false** ; les applications qui reçoivent des messages doivent définir cette valeur **true**. La raison est que souvent, les destinataires de messages sont moins nombreux que les expéditeurs. En fonction du nombre de destinataires, vous pouvez choisir d’utiliser une seule instance d’application pour gérer les tâches de siphon. L’utilisation de nombreux destinataires a un coût pour chaque file d’attente backlog.

Pour utiliser le code, créez une instance principale de [MessagingFactory][], une instance secondaire de [MessagingFactory][], une instance secondaire de [NamespaceManager][] instance et une instance de [SendAvailabilityPairedNamespaceOptions][]. L’appel peut se réduire à ce qui suit :

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions =
    new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Lorsque la tâche retournée par la méthode [PairNamespaceAsync][] se termine, tout est configuré et prêt à l’emploi. Avant que la tâche soit renvoyée, il se peut que vous n’ayez pas terminé toutes les tâches en arrière-plan nécessaires pour que l’association fonctionne correctement. Par conséquent, vous ne devez pas commencer à envoyer les messages avant que la tâche ne revienne. En cas de défaillance (informations d’identification incorrectes ou échec de création des files d’attente), ces exceptions seront levées lorsque la tâche est achevée. Une fois que la tâche est retournée, vérifiez que les files d’attente ont été détectées ou créées en examinant la propriété [BacklogQueueCount][] de votre instance [SendAvailabilityPairedNamespaceOptions][]. Pour le code qui précède, cette opération se présente comme suit :

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## Étapes suivantes

Maintenant que vous avez appris les principes fondamentaux de la messagerie asynchrone dans Service Bus, approfondissez le sujet sur [Noms d’espaces associés et conséquences en termes de coût].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Meilleures pratiques d’isolation des applications Service Bus des pannes et des sinistres]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [Noms d’espaces associés et conséquences en termes de coût]: service-bus-paired-namespaces.md

<!---HONumber=AcomDC_1210_2015-->