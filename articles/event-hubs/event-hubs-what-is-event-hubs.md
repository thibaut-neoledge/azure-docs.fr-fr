---
title: Qu’est-ce qu’Azure Event Hubs ? | Microsoft Docs
description: Vue d'ensemble et description de Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sethm

---
# Nouveautés des concentrateurs d'événements Azure ?
Les concentrateurs d'événements Azure sont un service d'entrée de données hautement évolutif qui peut traiter des millions d'événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données générées par vos périphériques connectés et vos applications. Event Hubs fonctionne comme la « porte d'entrée » d’un pipeline d’événements, et une fois que les données sont collectées dans un concentrateur d'événements, peut être transformées et stockées à l'aide de n'importe quel fournisseur d'analyse en temps réel ou d’adaptateurs de traitement par lot ou de stockage. Les concentrateurs d'événements dissocient la production d'un flux d'événements de la consommation de ces événements, de manière à ce que les consommateurs d'événements puissent accéder aux événements selon leur propre planification. Pour plus d’informations et de détails techniques, consultez la [Vue d’ensemble des hubs d’événements Azure](event-hubs-overview.md).

## Fonctionnalités des concentrateurs d'événements
Les hubs d’événements Azure constituent un service de traitement des événements et de la télémétrie à grande échelle, avec une faible latence et une grande fiabilité. Ce service est particulièrement utile pour :

* L’instrumentation de l'application
* L’expérience de l'utilisateur ou le traitement du flux de travail
* Des scénarios Internet des objets (IoT)

Certaines autres fonctionnalités clés de Event Hubs incluent le suivi du comportement dans les applications mobiles, les informations sur le trafic provenant de batteries de serveurs web, la capture d'événements de jeu dans les jeux de console ou la télémétrie recueillie sur des machines industrielles ou des véhicules connectés.

## Étapes suivantes
Pour plus d’informations sur les hubs d’événements, consultez les rubriques suivantes.

* [Vue d’ensemble des concentrateurs d’événements](event-hubs-overview.md)
* [Guide de programmation de concentrateurs d’événements](event-hubs-programming-guide.md)
* [Forum Aux Questions sur la disponibilité et le support des hubs d’événements](event-hubs-availability-and-support-faq.md)
* Prise en main avec un [didacticiel des concentrateurs d'événements][didacticiel des concentrateurs d'événements].
* Un [exemple d'application complet qui utilise des concentrateurs d’événements][exemple d'application complet qui utilise des concentrateurs d’événements].

[didacticiel des concentrateurs d'événements]: event-hubs-csharp-ephcs-getstarted.md
[exemple d'application complet qui utilise des concentrateurs d’événements]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097

<!---HONumber=AcomDC_0907_2016-->