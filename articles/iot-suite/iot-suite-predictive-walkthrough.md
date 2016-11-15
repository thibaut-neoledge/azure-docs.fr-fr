---
title: "Procédure pas à pas de maintenance prédictive | Microsoft Docs"
description: "Procédure pas à pas de la solution préconfigurée de maintenance prédictive Azure IoT."
services: 
suite: iot-suite
documentationcenter: 
author: aguilaaj
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2016
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ba48381f2c16a56e2e3f187017fbdbae09544e77


---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Procédure pas à pas de la solution préconfigurée de maintenance prédictive
## <a name="introduction"></a>Introduction
La solution préconfigurée de maintenance prédictive IoT Suite est une solution de bout en bout pour un scénario d’entreprise qui prévoit le moment où une défaillance est susceptible de se produire. Vous pouvez utiliser cette solution préconfigurée de manière proactive pour des activités telles que l’optimisation de la maintenance. La solution combine des services Azure IoT Suite clés et un espace de travail [Azure Machine Learning][lnk_machine_learning]. Cet espace de travail contient des expériences basées sur un échantillon de données publiques, permettant de prédire la durée de vie utile restante (VUR) d’un moteur d’avion. La solution implémente complètement le scénario professionnel IoT comme point de départ, pour vous permettre de planifier et de mettre en œuvre une solution qui réponde à vos besoins professionnels.

## <a name="logical-architecture"></a>Architecture logique
Le schéma suivant décrit les composants logiques de la solution préconfigurée :

![][img-architecture]

Les éléments en bleu sont des services Azure configurés dans l’emplacement que vous sélectionnez lorsque vous provisionnez la solution préconfigurée. Vous pouvez provisionner la solution préconfigurée si vous vous trouvez dans l’Est des États-Unis, l’Europe du Nord ou l’Asie de l’Est.

Certaines ressources ne sont pas disponibles dans les régions où vous provisionnez la solution préconfigurée. Les éléments en orange dans le schéma représentent les services Azure provisionnés dans la région la plus proche disponible (Sud-Centre des États-Unis, Europe occidentale ou Asie du Sud-Est) d’après la région sélectionnée.

L’élément en vert est un appareil simulé qui représente un moteur d’avion. Des informations supplémentaires sur ces appareils simulés sont disponibles dans la section suivante.

Les éléments en gris représentent des composants qui implémentent les fonctionnalités d’ *administration des appareils* . La version actuelle de la solution préconfigurée de maintenance prédictive ne provisionne pas ces ressources. Pour en savoir plus sur l’administration des appareils, reportez-vous à la [solution préconfigurée de surveillance à distance][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Simulations d’appareils
Dans la solution préconfigurée, un appareil simulé représente un moteur d’avion. La solution est configurée avec deux moteurs correspondant à un même avion. Chaque moteur émet quatre types de télémétrie (Capteur 9, Capteur 11, Capteur 14 et Capteur 15) qui fournissent les données requises par le modèle Machine Learning pour calculer la durée de vie utile restante du moteur. Chaque appareil simulé envoie les messages de télémétrie suivants à l’IoT Hub :

*Nombre de cycles*. Un cycle représente un vol d’une durée comprise entre 2 et 10 heures, dans lequel les données de télémétrie sont collectées toutes les demi-heures.

*Télémétrie*. Quatre capteurs représentent les attributs du moteur. Les capteurs sont nommés de manière générique Capteur 9, Capteur 11, Capteur 14 et Capteur 15. Ces 4 capteurs constituent une télémétrie suffisante pour obtenir des résultats utiles à partir du modèle Machine Learning pour la durée de vie utile restante. Ce modèle est créé à partir d’un jeu de données publiques qui inclut des données réelles de capteurs de moteurs. Pour plus d’informations sur la création du modèle à partir du jeu de données d’origine, consultez le [modèle de maintenance prédictive de la galerie Cortana Intelligence][lnk-cortana-analytics].

Les appareils simulés peuvent gérer les commandes suivantes envoyées à partir d’un IoT Hub :

| Commande | Description |
| --- | --- |
| StartTelemetry |Contrôle l’état de la simulation.<br/>Commande à l’appareil d’envoyer des données de télémétrie |
| StopTelemetry |Contrôle l’état de la simulation.<br/>Commande à l’appareil de cesser l’envoi de données de télémétrie |

IoT Hub fournit un accusé de réception de la commande de l’appareil.

## <a name="azure-stream-analytics-job"></a>Tâche Azure Stream Analytics
**Tâche : Télémétrie** agit sur le flux de télémétrie de l’appareil entrant à l’aide de deux instructions. La première sélectionne toutes les données de télémétrie à partir des appareils et envoie ces données au Blob Storage à partir duquel elles sont visualisées dans l’application web. La deuxième instruction calcule les valeurs de capteurs moyennes sur une fenêtre glissante de deux minutes et les envoie via l’Event Hub à un **processeur d’événements**.

## <a name="event-processor"></a>processeur d’événements
Le **processeur d’événements** mémorise les valeurs moyennes des capteurs sur un cycle terminé. Il les transmet à une API qui exécute le modèle Machine Learning pour calculer la durée de vie utile restante d’un moteur.

## <a name="azure-machine-learning"></a>Azure Machine Learning
Pour plus d’informations sur la création du modèle à partir du jeu de données d’origine, consultez le [modèle de maintenance prédictive de la galerie Cortana Intelligence][lnk-cortana-analytics].

## <a name="lets-start-walking"></a>Allons plus loin
Cette section vous guide à travers les composants de la solution et décrit l’utilisation prévue avec des exemples à l’appui.

### <a name="predictive-maintenance-dashboard"></a>Tableau de bord de maintenance prédictive
Cette page de l’application web utilise des contrôles Power BI JavaScript (consultez [Référentiel d’éléments visuels Power BI][lnk-powerbi]) pour visualiser :

* Les données de sortie des tâches Stream Analytics dans le Blob Storage.
* La durée de vie utile restante et le nombre de cycles d’un moteur d’avion.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observer le comportement de la solution cloud
Dans le portail Azure, accédez au groupe de ressources portant le nom de solution que vous avez choisi, pour afficher vos ressources configurées.

![][img-resource-group]

Lorsque vous approvisionnez la solution préconfigurée, vous recevez un e-mail contenant un lien vers l’espace de travail Machine Learning. Vous pouvez également accéder à l’espace de travail Machine Learning à partir de la page [azureiotsuite.com][lnk-azureiotsuite] de votre solution configurée, lorsqu’elle est à l’état **Prêt**.

![][img-machine-learning]

Dans le portail de la solution, vous pouvez voir que l’exemple est configuré avec quatre appareils simulés, correspondant à deux avions avec deux moteurs chacun et quatre capteurs par moteur. Lorsque vous accédez au portail de la solution pour la première fois, la simulation est arrêtée.

![][img-simulation-stopped]

Cliquez sur **Démarrer la simulation** pour commencer la simulation dans laquelle vous verrez l’historique des capteurs, la durée de vie utile restante, les cycles et l’historique de durée de vie utile restante s’afficher dans le tableau de bord.

![][img-simulation-running]

Lorsque la durée de vie utile restante est inférieure à 160 (seuil arbitraire choisi pour les besoins de la démonstration), le portail de la solution affiche un symbole d’avertissement en regard de la valeur correspondante et colore le moteur d’avion en jaune. Vous remarquerez que les valeurs de durée de vie utile restante tendent globalement à diminuer, mais avec des rebonds à la hausse ou à la baisse. Ce phénomène provient de la variabilité de la durée des cycles et de la précision du modèle.

![][img-simulation-warning]

La simulation complète prend environ 35 minutes pour effectuer 148 cycles. Le seuil de durée de vie utile restante de 160 est atteint pour la première fois à environ 5 minutes, et les deux moteurs atteignent le seuil à environ 8 minutes.

La simulation s’exécute sur le jeu de données complet pour les 148 cycles et se règle sur les valeurs finales de durée de vie utile restante et de cycles.

Vous pouvez arrêter la simulation à tout moment. L’option **Démarrer la simulation** réexécute la simulation à partir du début du jeu de données.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez exécuté la solution préconfigurée de maintenance prédictive souhaitée, si vous souhaitez la modifier, consultez [Personnalisation des solutions préconfigurées][lnk-customize].

Le billet de blog TechNet intitulé [IoT Suite - Under The Hood - Predictive Maintenance](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) (en anglais) fournit des détails supplémentaires sur la solution préconfigurée de maintenance prédictive.

Vous pouvez également explorer certaines des autres fonctionnalités et capacités des solutions préconfigurées IoT Suite :

* [Forum Aux Questions (FAQ) relatives à IoT Suite][lnk-faq]
* [Sécurité IoT depuis le début][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md



<!--HONumber=Nov16_HO2-->


