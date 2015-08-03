<properties
	pageTitle="Présentation de la messagerie Service Bus - Azure"
	description="Messagerie Service Bus : service flexible de distribution de données dans le cloud"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/02/2015"
	ms.author="sethm"/>


# Messagerie Service Bus : service flexible de distribution de données dans le cloud

La messagerie Service Bus est un service fiable de distribution des informations. L'objectif de ce service est de simplifier la communication. Quand plusieurs parties souhaitent échanger des informations, elles ont besoin d'un système de communication. La messagerie Service Bus est un système de communication tiers ou réparti, qui peut être comparé à un service postal classique. Le service postal permet aux clients d'envoyer facilement partout dans le monde toutes sortes de lettres et de paquets, avec différentes garanties de distribution au choix.

À l'instar du service postal de distribution de lettres, la messagerie Azure Service Bus offre un moyen flexible de distribuer des informations entre un l'expéditeur et le destinataire. Ce service de messagerie garantit la distribution des informations même si les deux parties concernées ne sont jamais en ligne en même temps ou si elles ne sont pas disponibles au même moment. C'est en cela que ce système de communication répartie s'apparente à l'envoi d'une lettre, tandis que la communication non répartie équivaut à un appel téléphonique traditionnel (quand il n'y avait pas encore de signal d'appel ni d'identification de l'appelant, qui sont plutôt des fonctionnalités de messagerie répartie).

L'expéditeur du message peut aussi choisir divers paramètres de distribution, notamment les transactions, la détection des doublons, l'expiration temporelle et le traitement par lot. Ces modèles présentent également certaines analogies avec le service postal : tentative renouvelée de distribution, signature obligatoire, changement d'adresse ou rappel.

La messagerie Service Bus comporte deux fonctionnalités distinctes : les files d'attente et les rubriques. Ces deux entités de messagerie prennent en charge tous les concepts décrits ci-dessus, parmi d'autres. La principale différence est que les rubriques prennent en charge les fonctionnalités de publication et d'abonnement qui peuvent être utilisées dans la logique de distribution et de routage basée sur du contenu sophistiqué, y compris l'envoi à plusieurs destinataires.

## Étapes suivantes

Pour en savoir plus sur la messagerie Service Bus, consultez les rubriques suivantes.

- [Présentation de l'architecture d'Azure Service Bus](fundamentals-service-bus-hybrid-solutions.md)

- [Utilisation des files d’attente Service Bus](service-bus-dotnet-how-to-use-queues.md)

- [Comment utiliser les rubriques Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
 

<!---HONumber=July15_HO4-->