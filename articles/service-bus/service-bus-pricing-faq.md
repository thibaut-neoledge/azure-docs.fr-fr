<properties 
   pageTitle="FAQ relatif à la tarification Service Bus | Microsoft Azure"
   description="Répond à certaines questions fréquemment posées sur la tarification de Service Bus."
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
   ms.date="09/09/2015"
   ms.author="sethm" />

# FAQ sur la tarification de Service Bus

Cette section répond à certaines questions fréquemment posées sur la tarification de Service Bus. Vous pouvez également visiter le [Forum aux questions de Support Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour obtenir des informations de tarification générale Microsoft Azure. Pour obtenir toutes les informations sur la tarification Service Bus, consultez la section [détails de tarification Service Bus](http://azure.microsoft.com/pricing/details/service-bus/).

>[AZURE.NOTE]Décrit la structure de prix pour les hubs d’événements dans la rubrique [FAQ relatif à la disponibilité et au support des hubs d’événements](event-hubs-availability-and-support-faq.md), et d’autres informations figurent dans la rubrique [Tarification des hubs d’événements](http://azure.microsoft.com/pricing/details/event-hubs/).

- [Quel est le coût de Service Bus ?](#how-do-you-charge-for-service-bus)
- [Quelle est l’utilisation de Service Bus soumise au transfert de données ? Laquelle ne l’est pas ?](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not)
- [Qu’est qu’un « relais » Service Bus exactement ?](#what-exactly-is-a-service-bus-quotrelayquot)
- [Comment le nombre d’heures de relais est-il calculé ?](#how-is-the-relay-hours-meter-calculated)
- [Que se passe-t-il si plusieurs écouteurs sont connectés à un relais donné ?](#what-if-i-have-more-than-one-listener-connected-to-a-given-relay)
- [Comment le nombre de messages de relais est-il calculé ?](#how-is-the-messages-meter-calculated-for-relays)
- [Service Bus facture-t-il le stockage ?](#does-service-bus-charge-for-storage)
- [Service Bus fixe-t-il des quotas d’utilisation ?](#does-service-bus-have-any-usage-quotas)

## Quel est le coût de Service Bus ?

Pour obtenir toutes les informations sur la tarification Service Bus, consultez la section [Tarification et facturation de Service Bus](https://msdn.microsoft.com/library/dn831889.aspx) et [Détails de la tarification Service Bus](http://azure.microsoft.com/pricing/details/service-bus/). Outre les prix indiqués, vous êtes facturé pour les transferts de données associés aux sorties à l’extérieur du centre de données dans lequel votre application est déployée. Vous trouverez plus de détails dans la rubrique [Quelle utilisation du Service Bus est soumise au transfert de données ? Laquelle ne l’est pas ?](#what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not) ci-dessous.

## Quelle est l’utilisation de Service Bus soumise au transfert de données ? Laquelle ne l’est pas ?

Les transferts de données au sein d’une région donnée d’Azure sont effectués gratuitement. Les transferts de données hors région sont soumis à des frais de sortie au tarif de 0,15 $ par Go depuis les régions Amérique du Nord et Europe et de 0,20 $ par Go depuis la région Asie-Pacifique. Les transferts de données entrants sont fournis gratuitement.

## Qu’est qu’un « relais » Service Bus exactement ?

Un relais est une entité Service Bus qui relaie les messages entre les clients et les services web. Le relais fournit le service avec une adresse Service Bus persistante, détectable, une connectivité fiable avec des fonctionnalités transversales de pare-feu/NAT ainsi que d’autres fonctions telles que l’équilibrage automatique. Un relais est explicitement instancié et ouvert à une adresse Service Bus (URL espaces de nom) lorsqu’un service WCF doté d’un relais, ou « écouteur de relais » se connecte à cette adresse pour la première fois. Les applications créent des écouteurs de relais à l’aide de l’API gérée .NET Service, qui fournit des versions prenant en charge les relais spéciaux des liaisons WCF standard.

## Comment le nombre d’heures de relais est-il calculé ?

Les heures de relais sont facturées pour la quantité cumulée de temps pendant laquelle le relais de Service Bus est « ouvert » pendant une période de facturation donnée. Un relais est explicitement instancié et ouvert à une adresse Service Bus (URL espaces de nom de service) lorsqu’un service WCF doté d’un relais, ou « écouteur de relais » se connecte à cette adresse pour la première fois. Le relais est fermé uniquement lorsque le dernier écouteur se déconnecte de son adresse. Par conséquent, pour la facturation, un relais est considéré comme « ouvert » dès le moment où le premier écouteur de relais se connecte, jusqu’au moment où l’écouteur de relais se déconnecte de l’adresse Service Bus du relais. En d’autres termes, un relais est considéré comme « ouvert » à chaque fois qu’un ou plusieurs écouteurs de relais sont connectés à l’adresse de Service Bus.

## Que se passe-t-il si plusieurs écouteurs sont connectés à un relais donné ?

Dans certains cas, un seul relais de Service Bus peut contenir plusieurs écouteurs. Cela peut se produire avec les services d’équilibrage de charge utilisant les liaisons **netTCPRelay** ou **HttpRelay**, ou avec les écouteurs d’événements de diffusion qui utilisent la liaison **netEventRelay**. Un relais Service Bus est considéré comme « ouvert » lorsqu’au moins un écouteur de relais est connecté à ce dernier. L’ajout des écouteurs supplémentaires à un relais ouvert ne modifie pas l’état de ce relais à des fins de facturation. Le nombre d’expéditeurs de relais (clients qui appellent ou envoient des messages aux relais) connectés à un relais n’a aucun effet sur le calcul des heures de relais.

## Comment le nombre de messages de relais est-il calculé ?

En général, les messages facturables des relais sont calculés à l’aide de la même méthode que ci-dessus pour les entités réparties (files d’attente, rubriques et abonnements). Toutefois, il existe plusieurs différences importantes :

1. L’envoi d’un message à un relais Service Bus est traité comme un envoi « complet » pour l’écouteur de relais qui reçoit le message, plutôt que comme un envoi au relais Service Bus suivi d’une livraison à l’écouteur de relais. Par conséquent, un appel de service de type demande-réponse (jusqu’à 64 Ko) par rapport à un écouteur de relais entraîne deux messages facturables : un message facturable pour la demande et un message facturable pour la réponse (en supposant que la réponse est également < = 64 Ko). Ceci diffère de l’utilisation d’une file d’attente servant d’intermédiaire entre un client et un service. Dans ce cas, le même modèle de demande-réponse nécessiterait un envoi de la demande à la file d’attente, puis un retrait de la file d’attente/une livraison depuis la file d’attente au service, suivi d’un envoi de réponse à une autre file d’attente et un retrait de la file d’attente/une livraison depuis cette file d’attente au client. L’utilisation du même principe (< = 64 Ko) concernant la taille pour le modèle de file d’attente par médiation générerait quatre messages facturables, le double du nombre facturé pour implémenter le même modèle à l’aide du relais. Bien sûr, il existe des avantages à utiliser les files d’attente pour atteindre ce modèle, par exemple, la durabilité et le nivellement de charge. Ces avantages peuvent justifier les dépenses supplémentaires.

2. Les relais ouverts à l’aide de la liaison WCF **netTCPRelay** traitent les messages non pas en tant que messages individuels, mais en tant que flux de données transitant par le système. En d’autres termes, seuls l’expéditeur et l’écouteur peuvent voir la trame du message individuel envoyé/reçus avec cette liaison. Par conséquent, pour les relais par liaison **netTCPRelay**, toutes les données sont traitées comme un flux de données pour le calcul des messages facturables. Dans ce cas, Service Bus calcule la quantité totale de données envoyées ou reçues via chaque relais individuel sur une base de 5 minutes et divise ce total par 64 Ko afin de déterminer le nombre de messages facturables pour le relais en question durant cette période.

## Service Bus facture-t-il le stockage ?

Non, Service Bus ne facture pas le stockage ? Toutefois, il existe un quota limitant la quantité maximale de données qui peuvent être conservées par la file d’attente/rubrique. Consultez le Forum aux questions suivants.

## Service Bus fixe-t-il des quotas d’utilisation ?

Par défaut, pour n’importe quel service cloud, Microsoft définit un quota d’utilisation agrégée mensuel calculé avec tous les abonnements d’un client. Nous savons que vous pouvez avoir des besoins dépassant ces limites, veuillez contacter le service clientèle à tout moment pour que nous puissions déterminer vos besoins et ajuster ces limites en conséquence. Pour Service Bus, les quotas d’utilisation d’agrégation sont les suivants :

- 5 milliards de messages

- 2 millions d’heures de relais

Nous nous réservons le droit de désactiver le compte d’un client ayant dépassé son quota d’utilisation pour un mois donné, nous enverrons une notification par messagerie électronique pour contacter un client avant d’exécuter une action quelconque. Les clients qui dépassent ces quotas sont responsables des frais qui dépassent ces quotas.

À l’instar d’autres services sur Azure, Service Bus applique un ensemble de quotas spécifiques pour assurer une utilisation juste des ressources. Les quotas d’utilisation appliqués par le service sont les suivantes :

- **Taille de la file d’attente/rubrique** – vous spécifiez la taille maximale de file d’attente ou de rubrique lors de la création de la file d’attente ou de la rubrique. Ce quota peut avoir une valeur de 1, 2, 3, 4 ou 5 Go. Si la taille de la file maximale est atteinte, les messages entrants supplémentaires sont rejetés et le code appelant reçoit une exception.

- **Nombre de connexions simultanées**
	- **File d’attente/Rubrique/Abonnement** - Le nombre de connexions TCP simultanées sur une file d’attente/rubrique/abonnement est limité à 100. Si ce quota est atteint, les requêtes suivantes pour des connexions supplémentaires sont rejetées et une exception sera reçue par le code appelant. Pour chaque structure de messagerie, Service Bus entretient une connexion TCP si l’un des clients créés par cette structure de messagerie ont une opération active en attente ou une opération terminée depuis moins de 60 secondes. Les opérations REST ne sont pas comptées parmi les connexions TCP simultanées.


- **Nombre d’écouteurs simultanés sur un relais** – le nombre d’écouteurs **netTcpRelay** et ** netHttpRelay ** sur un relais est limité à 25 (1 pour un relais **NetOneway**).

- **Nombre d’écouteurs de relais simultanés par espace de noms** – Service Bus impose une limite de 2 000 écouteurs de relais simultanés par espace de noms de service. Si ce quota est atteint, les requêtes suivantes concernant des ouvertures d’écouteurs de relais supplémentaires sont rejetées et une exception sera reçue par le code appelant.

- **Nombre de rubriques/files d’attente par espace de noms de service** – le nombre maximal de rubriques/files d’attente (entités soutenues par un stockage durable) sur un espace de noms de service est limité à 10 000. Si ce quota est atteint, les demandes de création de nouvelle rubrique/file d’attente dans l’espace de noms suivantes sont rejetées. Dans ce cas, le portail Azure affiche un message d’erreur ou le code client appelant reçoit une exception, selon que la tentative de création a été effectuée via le portail ou dans le code client.

- **Quotas de taille des messages**
	- **File d’attente/Rubrique/Abonnement**
		- **Taille de message** – chaque message est limité au total à 256 Ko, en-têtes de message compris.
		- **Taille de l’en-tête de message** – chaque en-tête de message est limité à 64 Ko.

	- **Relais NetOneway et NetEvent** – chaque message est limité au total à 64 Ko, en-têtes de message compris.
	- **Relais http et NetTcp** – Service Bus n’applique pas de limite supérieure à la taille de ces messages.

	Les messages qui dépassent ces quotas de taille sont rejetés et le code appelant reçoit une exception.

- **Nombre d’abonnements par rubrique** – le nombre maximal d’abonnements par rubrique est limité à 2 000. Si ce quota est atteint, les demandes de création d’abonnements supplémentaires à la rubrique seront rejetées. Dans ce cas, le portail de gestion affiche un message d’erreur ou le code client appelant reçoit une exception, selon que la tentative de création a été effectuée via le portail ou dans le code client.

- **Nombre de filtres SQL par rubrique** – le nombre maximal de filtres SQL par rubrique est limité à 2 000. Si ce quota est atteint, les requêtes de création de filtres supplémentaires sur la rubrique suivantes sont rejetées et une exception est reçue par le code appelant.

- **Nombre de filtres de corrélation par rubrique** – le nombre maximal de filtres de corrélation par rubrique est limité à 100 000. Si ce quota est atteint, les requêtes de création de filtres supplémentaires sur la rubrique suivantes sont rejetées et une exception est reçue par le code appelant.

Pour plus d’informations sur les quotas, consultez [Quotas Service Bus](service-bus-quotas.md).

## Étapes suivantes

Pour en savoir plus sur la messagerie Service Bus, consultez les rubriques suivantes.

- [Présentation de la messagerie Azure Service Bus Premium (billet de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Présentation de la messagerie Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
- [Présentation de l'architecture d'Azure Service Bus](fundamentals-service-bus-hybrid-solutions.md)
- [Utilisation des files d’attente Service Bus](service-bus-dotnet-how-to-use-queues.md)

<!---HONumber=Oct15_HO3-->