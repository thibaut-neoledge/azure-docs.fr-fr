---
title: "Procédure pas à pas de maintenance prédictive | Microsoft Docs"
description: "Procédure pas à pas de la solution préconfigurée de maintenance prédictive Azure IoT."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 9b2947d9ce00083c168635811395bc86b3e60b78
ms.lasthandoff: 04/26/2017


---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Procédure pas à pas de la solution préconfigurée de maintenance prédictive

## <a name="introduction"></a>Introduction

La solution préconfigurée de maintenance prédictive IoT Suite est une solution de bout en bout pour un scénario d’entreprise qui prévoit le moment auquel une défaillance est susceptible de se produire. Vous pouvez utiliser cette solution préconfigurée de manière proactive pour des activités telles que l’optimisation de la maintenance. La solution combine des services Azure IoT Suite clés, comme IoT Hub, Stream Analytics et un espace de travail [Azure Machine Learning][lnk-machine-learning]. Cet espace de travail contient un modèle basé sur un échantillon de données publiques, permettant de prédire la durée de vie utile restante (VUR) d’un moteur d’avion. La solution implémente complètement le scénario professionnel IoT comme point de départ, pour vous permettre de planifier et de mettre en œuvre une solution qui réponde à vos besoins professionnels.

## <a name="logical-architecture"></a>Architecture logique

Le schéma suivant décrit les composants logiques de la solution préconfigurée :

![][img-architecture]

Les éléments en bleu sont des services Azure configurés dans la région que vous sélectionnez lorsque vous provisionnez la solution préconfigurée. La liste des régions dans lesquelles vous pouvez déployer la solution préconfigurée apparaît sur la [page de configuration][lnk-azureiotsuite].

L’élément en vert est un appareil simulé qui représente un moteur d’avion. Des informations supplémentaires sur ces appareils simulés sont disponibles dans la section suivante.

Les éléments en gris représentent des composants qui implémentent les fonctionnalités de *gestion des appareils* . La version actuelle de la solution préconfigurée de maintenance prédictive ne provisionne pas ces ressources. Pour en savoir plus sur la gestion des appareils, reportez-vous à la [solution préconfigurée de surveillance à distance][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Simulations d’appareils

Dans la solution préconfigurée, un appareil simulé représente un moteur d’avion. La solution est configurée avec deux moteurs correspondant à un même avion. Chaque moteur émet quatre types de télémétrie (Capteur 9, Capteur 11, Capteur 14 et Capteur 15) qui fournissent les données requises par le modèle Machine Learning pour calculer la durée de vie utile restante du moteur. Chaque appareil simulé envoie les messages de télémétrie suivants à l’IoT Hub :

*Nombre de cycles*. Un cycle représente un vol d’une durée comprise entre deux et dix heures. Pendant le vol, les données de télémétrie sont collectées toutes les demi-heures.

*Télémétrie*. Quatre capteurs représentent les attributs du moteur. Les capteurs sont nommés de manière générique Capteur 9, Capteur 11, Capteur 14 et Capteur 15. Ces quatre capteurs constituent une télémétrie suffisante pour obtenir des résultats utiles à partir du modèle de durée de vie utile restante. Le modèle utilisé dans la solution préconfigurée est créé à partir d’un jeu de données publiques qui inclut des données réelles de capteurs de moteurs. Pour plus d’informations sur la création du modèle à partir du jeu de données d’origine, consultez le [modèle de maintenance prédictive de la galerie Cortana Intelligence][lnk-cortana-analytics].

Les appareils simulés peuvent prendre en charge les commandes suivantes, envoyées à partir d’IoT Hub dans la solution :

| Commande | Description |
| --- | --- |
| StartTelemetry |Contrôle l’état de la simulation.<br/>Commande à l’appareil d’envoyer des données de télémétrie |
| StopTelemetry |Contrôle l’état de la simulation.<br/>Commande à l’appareil de cesser l’envoi de données de télémétrie |

IoT Hub fournit un accusé de réception de la commande de l’appareil.

## <a name="azure-stream-analytics-job"></a>Tâche Azure Stream Analytics
**Tâche : Télémétrie** agit sur le flux de télémétrie de l’appareil entrant à l’aide de deux instructions. La première sélectionne toutes les données de télémétrie à partir des appareils et envoie ces données au Blob Storage à partir duquel elles sont visualisées dans l’application web. La deuxième instruction calcule les valeurs de capteurs moyennes sur une fenêtre glissante de deux minutes et les envoie via l’Event Hub à un **processeur d’événements**.

## <a name="event-processor"></a>processeur d’événements
**L’hôte de processeur d’événements** s’exécute dans une tâche web Azure. Le **processeur d’événements** mémorise les valeurs moyennes des capteurs sur un cycle terminé. Il les transmet à une API qui expose le modèle formé pour calculer la durée de vie utile restante d’un moteur. L’API est exposée par un espace de travail Machine Learning configuré dans le cadre de la solution.

## <a name="machine-learning"></a>Apprentissage automatique
Le composant Machine Learning utilise un modèle dérivé des données collectées à partir des moteurs d’avion réels. Vous pouvez accéder à l’espace de travail Machine Learning via la vignette sur la page [azureiotsuite.com][lnk-azureiotsuite] de votre solution provisionnée, lorsque son état est **Prêt**.


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les composants clés de la solution préconfigurée de maintenance prédictive, vous pouvez passer à la personnalisation. Consultez [Conseils sur la personnalisation des solutions préconfigurées][lnk-customize].

Vous pouvez également explorer certaines des autres fonctionnalités et capacités des solutions préconfigurées IoT Suite :

* [Forum Aux Questions (FAQ) relatives à IoT Suite][lnk-faq]
* [Sécurisation de l’Internet des objets de bout en bout][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
