<properties 
   pageTitle="Architecture Service Bus | Microsoft Azure"
   description="Décrit l'architecture de traitement de message d'Azure Service Bus."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/06/2015"
   ms.author="sethm" />

# Architecture de Service Bus

Cet article décrit l’architecture de traitement de message d’Azure Service Bus.

## Unités d'échelle de Service Bus

Service Bus est organisé par *unités d'échelle*. Une unité d'échelle est une unité de déploiement et contient tous les composants requis pour exécuter le service. Chaque région déploie une ou plusieurs unités d'échelle Service Bus.

Un espace de noms Service Bus est mappé à une unité d'échelle. L’unité d’échelle gère tous les types d’entités Service Bus : relais, entités de messagerie répartie (files d’attente, rubriques, abonnements). Une unité d'échelle Service Bus est constituée des éléments suivants :

- **Un ensemble de nœuds de passerelle.** Les nœuds de passerelle authentifient les requêtes entrantes et gèrent les requêtes de relais. Chaque nœud de passerelle a une adresse IP publique.

- **Un ensemble de nœuds de broker de messagerie.** Les nœuds de broker de messagerie traitent les requêtes concernant les entités de messagerie.

- **Un ensemble de nœuds de notification.** Les nœuds de notification envoient des notifications Push à tous les appareils inscrits.

- **Une banque de passerelle.** La banque de passerelle conserve les données pour chaque entité définie dans cette unité d'échelle. La banque de passerelle est implémentée sur une base de données SQL Azure.

- **Plusieurs banques de messagerie.** Les banques de messagerie conservent les messages de l'ensemble des files d'attente, rubriques et abonnements qui sont définis dans cette unité d'échelle. Elles contiennent également toutes les données d'abonnement. Une file d’attente ou une rubrique est mappée à une banque de messagerie, sauf si l’option [Entités de messagerie partitionnées](service-bus-partitioning.md) est activée. Les abonnements sont stockés dans la même banque de messagerie que leur rubrique parent. Sauf pour Service Bus [Premium Messaging](service-bus-premium-messaging.md), les banques de messagerie sont implémentées sur des bases de données SQL Azure.

- **Plusieurs magasins d'inscriptions.** Les magasins des inscriptions contiennent les inscriptions des appareils pour tous les concentrateurs de notification qui sont définis dans cette unité d'échelle. Les magasins des inscriptions sont implémentés sur des bases de données SQL Azure.

## Conteneurs

Un conteneur spécifique est assigné à chaque entité de messagerie. Un conteneur est une construction logique qui utilise exactement une banque de messagerie pour stocker toutes les données pertinentes pour ce conteneur. Chaque conteneur est affecté à un nœud de broker de messagerie. En règle générale, il existe plus de conteneurs que de nœuds de broker de messagerie. Par conséquent, chaque nœud de broker de messagerie charge plusieurs conteneurs. La distribution de conteneurs sur un nœud de broker de messagerie est organisée de façon à ce que tous les nœuds de broker de messagerie soient chargés de façon égale. Si le modèle de charge change (par exemple, un des conteneurs devient très occupé) ou si un nœud de broker de messagerie devient temporairement indisponible, les conteneurs sont redistribués entre les nœuds de broker de messagerie.

## Traitement des requêtes de messagerie entrantes

Lorsqu'un client envoie une requête à Service Bus, l'équilibrage de charge Azure la transmet à l'un des nœuds de passerelle. Le nœud de passerelle autorise la requête. Si la requête concerne une entité de messagerie (file d'attente, rubrique, abonnement), le nœud de passerelle recherche l'entité dans le magasin de passerelle et détermine dans quelle banque de messagerie se trouve l'entité. Il recherche ensuite quel nœud de broker de messagerie traite actuellement ce conteneur et envoie la requête à ce nœud de broker de messagerie. Le nœud de broker de messagerie traite la requête et met à jour l'état de l'entité dans le magasin de conteneur. Le nœud de broker de messagerie envoie ensuite la réponse au nœud de passerelle, qui envoie une réponse appropriée au client qui a émis la requête d'origine.

![Traitement des requêtes de messagerie entrantes](./media/service-bus-architecture/IC690644.png)

## Traitement des requêtes de relais entrantes

Lorsqu'un client envoie une requête à Service Bus, l'équilibrage de charge Azure la transmet à l'un des nœuds de passerelle. Si la requête est une requête d'écoute, le nœud de passerelle crée un relais. Si la requête est une requête de connexion à un relais spécifique, le nœud de passerelle transfère la requête de connexion au nœud de passerelle qui possède le relais. Le nœud de passerelle qui possède le relais envoie une requête de rendez-vous au client d'écoute, lui demandant de créer un canal temporaire au nœud de passerelle qui a reçu la requête de connexion.

Lorsque la connexion au relais est établie, les clients peuvent échanger des messages via le nœud de passerelle utilisé pour le rendez-vous.

![Traitement des requêtes de relais entrantes](./media/service-bus-architecture/IC690645.png)

## Traitement des requêtes du concentrateur de notification entrantes

Lorsqu'un client envoie une requête à Service Bus, l'équilibrage de charge Azure la transmet à l'un des nœuds de passerelle. Si la requête est une inscription d'appareil pour un concentrateur de notification existant, le nœud de passerelle écrit l'inscription dans le magasin des inscriptions et envoie une réponse au dispositif appelant. Si la requête est un message de notification, le nœud de passerelle place le message dans une file d'attente de notification. Un des nœuds de notification retire le message de la file d'attente de notification et envoie le message à tous les appareils qui sont inscrits dans le magasin des inscriptions. Si un message doit être reçu par un grand nombre d'appareils, plusieurs nœuds de notification participent à l'envoi des messages aux appareils.

![Traitement des requêtes du concentrateur de notification entrantes](./media/service-bus-architecture/IC690646.png)

## Étapes suivantes

Maintenant que vous avez lu une présentation du fonctionnement de Service Bus, consultez les liens suivants :

- [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
- [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Une solution de messages de file d'attente utilisant les files d'attente Service Bus](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)

<!---HONumber=AcomDC_0218_2016-->