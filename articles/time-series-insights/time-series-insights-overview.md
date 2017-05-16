---
title: "Vue d’ensemble d’Azure Time Series Insights | Microsoft Docs"
description: "Présentation d’Azure Time Series Insights, qui est un nouveau service d’analyse de données de série chronologique et de solutions IoT"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: cc35c00bec18ee1d9ed52b29b68ddacc1136dc70
ms.lasthandoff: 04/26/2017

---

# <a name="overview-of-azure-time-series-insights"></a>Vue d’ensemble d’Azure Time Series Insights

Azure Time Series Insights est un service cloud entièrement géré avec des composants de stockage, d’analyse et de visualisation qui le rendent incroyablement simple pour recevoir, stocker, explorer et analyser simultanément des milliards d’événements. Time Series Insights offre une vue globale de vos données, ce qui vous permet de valider rapidement vos solution IoT et d’éviter les temps d’arrêt coûteux sur les appareils stratégiques en vous aidant à identifier les tendances et anomalies cachées et à effectuer des analyses de la cause première en temps presque réel. Temps série Insights reçoit des données de série chronologique de services Broker pour les événements (par exemple, IoT Hubs ou concentrateurs d’événements), indexe les données et supprime les données en fonction d’une stratégie de rétention configurable. Les utilisateurs exploitent les données pour leur scénario d’entreprise via une expérience utilisateur ou des API REST de requête intuitives.

![Vue d’ensemble de Time Series Insights](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Principaux scénarios

* Validation et surveillance des solutions IoT en quelques minutes
* Visualisation et analyse intuitives de données IoT à grande échelle
* Accélération de l’analyse de la cause première et de la détection des anomalies
* Création d’une vue globale de plusieurs périphériques, sites et données

## <a name="key-capabilities-and-benefits"></a>Avantages et fonctionnalités clés

* **Prise en main facile** : Azure Time Series Insights ne requiert aucune préparation de données initiales et est extrêmement rapide. Vous pouvez ainsi vous connecter à des milliards d’événements de votre Azure IoT Hub ou concentrateur d’événements en quelques minutes. Une fois connecté, vous pouvez visualiser et utiliser les données de vos capteurs en quelques secondes pour valider rapidement votre solution IoT. Time Series Insights est extrêmement intuitif et facile à utilise. Vous pouvez interagir avec vos données sans avoir à écrire une seule ligne de code.  Vous n’avez pas besoin non plus d’apprendre un nouveau langage. Time Series Insights propose aux utilisateurs expérimentés une zone de recherche de texte libre granulaire, ainsi qu’une expérience d’exploration « pointer et cliquer » pour les novices.

* **Aperçu en temps presque réel en quelques secondes** : optimisez vos données de série chronologique avec les composants de stockage, d’analyse et de visualisation de Time Series Insights, tous disponibles au même endroit. Time Series Insights peut recevoir des centaines de millions d’événements de capteur par jour, avec une latence d’une minute. Vous pouvez ainsi réagir rapidement aux changements. Time Series Insights vous permet d’étudier de manière approfondie vos données de capteur en vous aidant à identifier rapidement les tendances et anomalies, vous permettant ainsi d’effectuer facilement des analyses de la cause première complexes et d’éviter des interruptions de service coûteuses. En proposant une corrélation croisée entre données en temps réel et données historiques, Time Series Insights permet aux utilisateurs de détecter les tendances cachées dans leurs données.

* **Créer des solutions personnalisées** : intégrez les données Azure Time Series Insights à vos applications existantes ou créez de nouvelles solutions personnalisées avec les API REST de Time Series Insights.  De plus, la création et le partage de vues personnalisées sont un jeu d’enfant, permettant aux autres utilisateurs d’étudier plus facilement vos découvertes.

* **Évolutivité** : Time Series Insights est conçu pour prendre en charge l’IoT. Dans la version préliminaire, de 1 à 100 millions d’événements peuvent être entrés par jour, avec une durée de conservation par défaut de 31 jours. Time Series Insights permet aux utilisateurs de visualiser et d’analyser les flux de données en direct en temps presque réel, en plus de quantités importantes de données historiques. À l’avenir, les taux d’entrée et de conservation seront augmentés pour s’adapter à l’évolution constante de l’entreprise.

## <a name="taxonomy-of-time-series-insights"></a>Taxonomie de Time Series Insights

* **Environnement** : un environnement Time Series est une ressource Azure disposant d’une capacité d’entrée et de stockage.  Les clients approvisionne des environnements via le portail Azure avec la capacité requise.
* **Source d’événement** : une source d’événement Time Series Insights est dérivée d’un service Broker pour les événements tel que les concentrateurs d’événements Azure.  Time Series Insights se connecte directement aux sources d’événement, en recevant le flux de données sans demander aux utilisateurs d’écrire une seule ligne de code. Time Series Insights prend actuellement en charge les concentrateurs d’événements Azure et Azure IoT Hubs. D’autres sources d’événement seront ajoutées à l’avenir.
* **Données de référence** : Time Series Insights permet aux utilisateurs d’associer des données de série chronologique à des données de référence.  Les données de référence peuvent inclure des métadonnées sur les périphériques ou d’autres données statiques qui changent relativement peu souvent. Time Series Insights associer les données de référence avec des flux de données, permettant ainsi aux utilisateurs de visualiser et d’analyser ces données en temps presque réel.

