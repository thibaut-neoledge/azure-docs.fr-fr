<properties
	pageTitle="Qu’est-ce qu’Azure Event Hubs ? | Microsoft Azure"
	description="Vue d'ensemble et description de Azure Event Hubs"
	services="event-hubs"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/12/2016"
	ms.author="sethm"/>

# Nouveautés des concentrateurs d'événements Azure ?

Les concentrateurs d'événements Azure sont un service d'entrée de données hautement évolutif qui peut traiter des millions d'événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données générées par vos périphériques connectés et vos applications. Event Hubs fonctionne comme la « porte d'entrée » d’un pipeline d’événements, et une fois que les données sont collectées dans un concentrateur d'événements, peut être transformées et stockées à l'aide de n'importe quel fournisseur d'analyse en temps réel ou d’adaptateurs de traitement par lot ou de stockage. Les concentrateurs d'événements dissocient la production d'un flux d'événements de la consommation de ces événements, de manière à ce que les consommateurs d'événements puissent accéder aux événements selon leur propre planification.

## Fonctionnalités des concentrateurs d'événements

Les concentrateurs d'événements Azure constituent un service de traitement des événements. Celui-ci fournit des entrées d'événements et de télémétrie vers le cloud à grande échelle, avec une faible latence et une grande fiabilité. Ce service est particulièrement utile pour :

- L’instrumentation de l'application
- L’expérience de l'utilisateur ou le traitement du flux de travail
- Des scénarios Internet des objets (IoT)

Certaines autres fonctionnalités clés de Event Hubs incluent le suivi du comportement dans les applications mobiles, les informations sur le trafic provenant de batteries de serveurs web, la capture d'événements de jeu dans les jeux de console ou la télémétrie recueillie sur des machines industrielles ou des véhicules connectés.

Contrairement aux [rubriques et files d'attente Service Bus](../service-bus/service-bus-messaging-overview.md), les concentrateurs d'événements servent à fournir des flux de messagerie gérés à grande échelle ? Les capacités de Event Hubs, par exemple, des rubriques Service Bus dans la mesure où elles sont fortement orientées vers le débit élevé et les scénarios de traitement des événements. Par conséquent, les concentrateurs d'événements n'implémentent pas certaines des fonctionnalités de messagerie qui sont disponibles pour les [rubriques](../service-bus/service-bus-fundamentals-hybrid-solutions.md#topics). Si vous avez besoin de ces fonctionnalités, les rubriques constituent le meilleur choix.

## Étapes suivantes

Pour obtenir des informations détaillées sur les concentrateurs d'événements, consultez les rubriques suivantes.

- [Vue d’ensemble des concentrateurs d’événements](event-hubs-overview.md)
- [Guide de programmation de concentrateurs d’événements](event-hubs-programming-guide.md)
- [Forum Aux Questions sur la disponibilité et le support des hubs d’événements](event-hubs-availability-and-support-faq.md)
- Prise en main avec un [didacticiel des concentrateurs d'événements][].
- Un [exemple d'application complet qui utilise des concentrateurs d’événements][].

[didacticiel des concentrateurs d'événements]: event-hubs-csharp-ephcs-getstarted.md
[exemple d'application complet qui utilise des concentrateurs d’événements]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097

<!---HONumber=AcomDC_0706_2016-->