<properties
	pageTitle="Présentation de la messagerie Service Bus | Microsoft Azure"
	description="Messagerie Service Bus : service flexible de distribution de données dans le cloud"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="03/09/2016"
	ms.author="sethm"/>


# Messagerie Service Bus : service flexible de distribution de données dans le cloud

La messagerie Service Bus Azure est un service fiable de distribution des informations. L'objectif de ce service est de simplifier la communication. Quand plusieurs parties souhaitent échanger des informations, elles ont besoin d'un système de communication. La messagerie Service Bus est un système de communication tiers ou réparti, qui peut être comparé à un service postal classique. Le service postal permet aux clients d'envoyer facilement partout dans le monde toutes sortes de lettres et de paquets, avec différentes garanties de distribution au choix.

À l’instar du service postal de distribution du courrier, la messagerie Service Bus constitue un moyen flexible de distribuer des informations entre l’expéditeur et le destinataire. Ce service de messagerie garantit la distribution des informations même si les deux parties concernées ne sont jamais en ligne en même temps ou si elles ne sont pas disponibles au même moment. C'est en cela que ce système de communication répartie s'apparente à l'envoi d'une lettre, tandis que la communication non répartie équivaut à un appel téléphonique traditionnel (quand il n'y avait pas encore de signal d'appel ni d'identification de l'appelant, qui sont plutôt des fonctionnalités de messagerie répartie).

L’expéditeur du message peut aussi choisir divers paramètres de distribution, notamment les transactions, la détection des doublons, l’expiration temporelle et le traitement par lot. Ces modèles présentent également certaines analogies avec le service postal : tentative renouvelée de distribution, signature obligatoire, changement d'adresse ou rappel.

Service Bus prend en charge deux modèles de messagerie distincts : la messagerie *relayée* et la messagerie *répartie*.

## Messagerie relayée

Le composant [relais](service-bus-relay-overview.md) de Service Bus est un service centralisé (mais très équilibré) qui prend en charge différents protocoles de transfert et normes de services web. Cela inclut SOAP, WS-* et même REST. Le [service de relais](service-bus-dotnet-how-to-use-relay.md) fournit une gamme d’options de connectivité relais différentes et aide à négocier des connexions directes peer-to-peer quand c’est possible. Service Bus est optimisé pour les développeurs .NET qui utilisent Windows Communication Foundation (WCF), en ce qui concerne à la fois les performances et la convivialité, et fournit un accès complet à son service de relais via des interfaces SOAP et REST. Cela rend possible l’intégration de n'importe quel environnement de programmation SOAP ou REST à Service Bus.

Le service de relais prend en charge la messagerie unidirectionnelle standard, la messagerie demande/réponse et la messagerie d’homologue à homologue. Il prend également en charge la distribution des événements sur Internet pour activer les scénarios de publication/abonnement et la communication par socket bidirectionnelle pour une efficacité accrue de point à point. Dans le modèle de messagerie par relais, un service local se connecte au service de relais via un port sortant et crée un socket bidirectionnel lié à des adresses de rendez-vous spécifiques. Le client peut ensuite communiquer avec le service local en envoyant des messages au service de relais en ciblant l'adresse de rendezvous. Le service de relais « relayera » ensuite les messages au service local via le socket bidirectionnel déjà en place. Le client n’a pas besoin d’une connexion directe au service local ni de savoir où se trouve le service, et le service local n’a pas besoin d’ouvrir de ports entrants sur le pare-feu.

Vous devez lancer la connexion entre votre service local et le service de relais à l’aide d’une suite de liaisons de « relais » WCF. En coulisses, les liaisons de relais se mappent à des éléments de liaison de transport destinés à créer des composants de canal WCF qui s’intègrent à Service Bus dans le cloud.

La messagerie relayée offre de nombreux avantages, mais nécessite que le serveur et le client soient en ligne en même temps pour envoyer et recevoir des messages. Cela n’est pas optimal pour la communication de style HTTP, dans laquelle les requêtes ne sont généralement pas durables, ni pour les clients qui ne se connectent qu’occasionnellement, comme les navigateurs, les applications mobiles et ainsi de suite. La messagerie répartie prend en charge la communication répartie et a ses propres avantages ; les clients et les serveurs peuvent se connecter lorsque c’est nécessaire et effectuer leurs opérations de manière asynchrone.

## Messagerie répartie

Contrairement au modèle de la messagerie relayée, la [messagerie répartie](service-bus-fundamentals-hybrid-solutions.md) peut être considérée comme asynchrone, ou « temporellement découplée ». Les producteurs (expéditeurs) et les consommateurs (récepteurs) n'ont pas besoin d’être en ligne en même temps. L'infrastructure de la messagerie stocke les messages de façon fiable dans un « broker » (par exemple, une file d'attente) jusqu'à ce que le récepteur soit prêt à les recevoir. Ceci permet aux composants de l'application distribuée d'être déconnectés, soit volontairement, par exemple pour des raisons de maintenance, soit en raison de l'échec d'un composant, sans conséquences sur le système dans sa globalité. En outre, il se peut que l'application réceptrice n’ait besoin d’être en ligne qu’à certaines heures de la journée (par exemple, un système de gestion d'inventaire dont l’exécution n’est requise qu’en fin de journée).

Les principaux composants de l'infrastructure de la messagerie répartie Service Bus sont des files d'attente, des rubriques et des abonnements. La principale différence est que les rubriques prennent en charge les fonctionnalités de publication et d'abonnement qui peuvent être utilisées dans la logique de distribution et de routage basée sur du contenu sophistiqué, y compris l'envoi à plusieurs destinataires. Ces composants donnent accès à de nouveaux scénarios de messagerie asynchrone, comme le découplage temporel, la publication/abonnement et l'équilibrage de charge. Pour plus d’informations sur ces éléments de messagerie, consultez [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md).

Comme pour l'infrastructure de messagerie relayée, la fonctionnalité de messagerie répartie est destinée aux programmeurs WCF et .NET Framework, ainsi que via REST.

## Étapes suivantes

Pour en savoir plus sur la messagerie Service Bus, consultez les rubriques suivantes.

- [Files d'attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
- [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Architecture de Service Bus](service-bus-architecture.md)
- [Utilisation des files d’attente Service Bus](service-bus-dotnet-how-to-use-queues.md)
- [Comment utiliser les rubriques Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
 

<!---HONumber=AcomDC_0316_2016-->