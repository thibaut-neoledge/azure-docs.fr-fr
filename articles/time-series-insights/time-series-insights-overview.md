---
title: "Vue d’ensemble d’Azure Time Series Insights | Microsoft Docs"
description: "Présentation d’Azure Time Series Insights, un nouveau service d’analyse de données de série chronologique et de solutions IoT"
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: abd66208ab7ac30831f3f1eddb2891ed7bcd3995
ms.contentlocale: fr-fr
ms.lasthandoff: 07/21/2017

---

# <a name="what-is-azure-time-series-insights"></a>Qu’est-ce que Azure Time Series Insights

Azure Time Series Insights est un service cloud géré avec des composants de stockage, d’analyse et de visualisation qui le rendent simple pour recevoir, stocker, explorer et analyser simultanément des milliards d’événements. Time Series Insights offre une vue globale de vos données, ce qui vous permet de valider rapidement vos solutions IoT et d’éviter les temps d’arrêt coûteux sur les appareils en vous aidant à identifier les tendances et anomalies cachées et à effectuer des analyses de la cause première en temps presque réel. Temps série Insights reçoit des données de série chronologique de services Broker pour les événements (par exemple, IoT Hubs ou concentrateurs d’événements), indexe les données et supprime les données en fonction d’une stratégie de rétention configurable. Les utilisateurs exploitent les données via une expérience utilisateur ou des API REST de requête intuitives.

![Vue d’ensemble de Time Series Insights](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Principaux scénarios

* Surveillez et validez des solutions de IoT en quelques minutes.
* Visualisez et analysez les données IoT mises à l’échelle.
* Accélérez l’analyse de la cause première et de la détection des anomalies.
* Créez une vue globale de plusieurs périphériques, sites et données.

## <a name="capabilities-and-benefits"></a>Avantages et fonctionnalités clés

* **Prise en main facile** : Azure Time Series Insights ne requiert aucune préparation de données initiale en plus d’être extrêmement rapide. Se connecter à des milliards d’événements dans votre IoT Hub Azure ou votre Event Hub en quelques minutes. Une fois connecté, visualisez et interagissez avec les données de capteurs en quelques secondes pour valider rapidement vos solutions IoT. Time Series Insights est facile à utiliser. Vous pouvez interagir avec vos données sans écrire une seule ligne de code.  Vous n’avez pas besoin d’apprendre un nouveau langage. Time Series Insights propose aux utilisateurs expérimentés une zone de recherche de texte libre granulaire, ainsi qu’une expérience d’exploration « pointer et cliquer » pour tous.

* **Informations en temps presque réel** : Time Series Insights peut recevoir des centaines de millions d’événements de capteur par jour, avec une latence d’une minute. Vous pouvez ainsi réagir rapidement aux changements. Time Series Insights vous aide à obtenir des informations approfondies sur vos données de capteur en vous aidant à identifier rapidement les tendances et anomalies rapidement, à effectuer des analyses de la cause première complexes et à éviter des interruptions de service coûteuses. En proposant une corrélation croisée entre données en temps réel et données historiques, Time Series Insights vous aide à détecter les tendances cachées dans leurs données.

* **Créer des solutions personnalisées** : intégrez les données Azure Time Series Insights à vos applications existantes ou créez de nouvelles solutions personnalisées avec les API REST de Time Series Insights. Créez et partagez des affichages personnalisés que vous pouvez partager afin que d’autres personnes puissent explorer vos découvertes.

* **Extensibilité** : Time Series Insights est conçu pour prendre en charge IoT une fois mis à l’échelle. Dans la version préliminaire, de 1 à 100 millions d’événements peuvent être entrés par jour, avec une durée de conservation par défaut de 31 jours. Vous pouvez visualiser et analyser les flux de données en direct en temps presque réel, en plus de quantités importantes de données historiques. À l’avenir, les taux d’entrée et de rétention seront augmentés pour s’adapter à l’évolution constante de l’entreprise.

## <a name="time-series-insights-glossary"></a>Glossaire Time Series Insights

* **Environnement** : un environnement Time Series est une ressource Azure disposant d’une capacité d’entrée et de stockage.  Les clients approvisionnent des environnements via le portail Azure avec la capacité requise.
* **Source d’événement** : une source d’événement Time Series Insights est dérivée d’un service Broker pour les événements tel que les concentrateurs d’événements Azure.  Time Series Insights se connecte directement aux sources d’événement, en recevant le flux de données sans écrire une seule ligne de code. Actuellement, Time Series Insights prend en charge les concentrateurs d’événements Azure et les IoT Hubs.
* **Données de référence** : Time Series Insights permet aux utilisateurs d’associer des données de série chronologique à des données de référence.  Les données de référence peuvent inclure des métadonnées sur les périphériques ou d’autres données statiques qui changent relativement peu souvent. Time Series Insights associer les données de référence avec des flux de données, permettant ainsi aux utilisateurs de visualiser et d’analyser ces données en temps presque réel.

