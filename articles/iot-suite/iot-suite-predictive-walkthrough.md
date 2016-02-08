<properties
 pageTitle="Procédure pas à pas de maintenance prédictive | Microsoft Azure"
 description="Procédure pas à pas de la solution préconfigurée de maintenance prédictive Azure IoT."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="12/02/2015"
 ms.author="araguila"/>

# Procédure pas à pas de la solution préconfigurée de maintenance prédictive

## Introduction

La solution préconfigurée de maintenance prédictive IoT Suite est une solution de bout en bout pour un scénario d’entreprise qui prévoit le moment où une défaillance est susceptible de se produire. Vous pouvez utiliser cette solution préconfigurée de manière proactive pour les activités telles que l’optimisation de la maintenance. La solution combine des services Azure IoT Suite clés, y compris un espace de travail [Azure Machine Learning][lnk_machine_learning] doté d’expériences permettant de prédire la durée de vie utile restante d’un moteur d’avion en s’appuyant sur un exemple de jeu de données publiques. La solution fournit une implémentation complète du scénario d’entreprise en guise de point de départ afin de vous permettre de planifier et d’implémenter ce type de solution IoT pour répondre à vos propres besoins professionnels spécifiques.

## Architecture logique

Le schéma suivant décrit les composants logiques de la solution préconfigurée :

![][img-architecture]

Les éléments en bleu sont des services Azure configurés dans l’emplacement que vous sélectionnez lorsque vous provisionnez la solution préconfigurée. Vous pouvez provisionner la solution préconfigurée si vous vous trouvez dans l’Est des États-Unis, l’Europe du Nord ou l’Asie de l’Est.

Certaines ressources ne sont pas disponibles dans les régions où vous provisionnez la solution préconfigurée. Les éléments en orange dans le schéma représentent les services Azure provisionnés dans la région la plus proche disponible (Sud-Centre des États-Unis, Europe occidentale ou Asie du Sud-Est) d’après la région sélectionnée.

L’élément en vert est un appareil simulé qui représente un moteur d’avion. Des informations supplémentaires sur ces appareils simulés sont disponibles ci-dessous.

Les éléments en gris représentent des composants qui implémentent les fonctionnalités d’*administration des appareils*. La version actuelle de la solution préconfigurée de maintenance prédictive ne provisionne pas ces ressources. Pour en savoir plus sur l’administration des appareils, reportez-vous à la [solution préconfigurée de surveillance à distance][lnk-remote-monitoring].

## Simulations d’appareils

Dans la solution préconfigurée, un appareil simulé représente un moteur d’avion. La solution est provisionnée avec 2 moteurs qui mappent vers un seul avion. Chaque moteur émet 4 types de télémétrie (Capteur 9, Capteur 11, Capteur 14 et Capteur 15) qui fournissent les données requises par le modèle Machine Learning pour calculer la durée de vie utile restante de ce moteur. Chaque appareil simulé envoie les messages de télémétrie suivants à l’IoT Hub :

*Nombre de cycles*. Un cycle représente un vol effectué d’une durée variable de 2 à 10 heures, dans lequel les données de télémétrie sont capturées toutes les demi-heures pendant la durée du vol.

*Télémétrie*. Il existe 4 capteurs qui représentent les attributs du moteur. Les capteurs sont nommés de manière générique Capteur 9, Capteur 11, Capteur 14 et Capteur 15. Ces 4 capteurs constituent une télémétrie suffisante pour obtenir des résultats utiles à partir du modèle Machine Learning pour la durée de vie utile restante. Ce modèle est créé à partir d’un jeu de données publiques qui inclut des données réelles de capteurs de moteurs. Pour plus d’informations sur la création du modèle à partir du jeu de données d’origine, consultez [Modèle de maintenance prédictive de la galerie Cortana Analytics][lnk-cortana-analytics].

Les appareils simulés peuvent gérer les commandes suivantes envoyées à partir d’un IoT Hub :

| Commande | Description |
|---------|-------------|
| StartTelemetry | Contrôle l’état de la simulation.<br/>Commande à l’appareil d’envoyer des données de télémétrie |
| StopTelemetry | Contrôle l’état de la simulation.<br/>Commande à l’appareil de cesser l’envoi de données de télémétrie |

IoT Hub fournit un accusé de réception de la commande de l’appareil.

## Tâche Azure Stream Analytics

La **Tâche : Télémétrie** agit sur le flux de télémétrie de l’appareil entrant à l’aide de deux instructions. La première sélectionne toutes les données de télémétrie à partir des appareils et envoie ces données au Blob Storage à partir duquel elles sont visualisées dans l’application web. La deuxième instruction calcule les valeurs de capteurs moyennes sur une fenêtre glissante de deux minutes et les envoie via l’Event Hub à un **processeur d’événements**.

## Processeur d’événements

Le **processeur d’événements** prend les valeurs de capteurs moyennes d’un cycle terminé et les transmet à une API qui expose le modèle formé Machine Learning pour calculer la durée de vie utile restante d’un moteur.

## Azure Machine Learning

Pour plus d’informations sur la création du modèle à partir du jeu de données d’origine, consultez [Modèle de maintenance prédictive de la galerie Cortana Analytics][lnk-cortana-analytics].

## Allons plus loin

Cette section vous guide à travers les composants de la solution et décrit l’utilisation prévue avec des exemples à l’appui.

### Tableau de bord de maintenance prédictive

Cette page de l’application web utilise des contrôles Power BI JavaScript (consultez [Référentiel d’éléments visuels Power BI][lnk-powerbi]) pour visualiser :

- Les données de sortie des tâches Stream Analytics dans le Blob Storage.
- La durée de vie utile restante et le nombre de cycles d’un moteur d’avion.

### Observer le comportement de la solution cloud

Vous pouvez afficher vos ressources approvisionnées depuis le portail Azure, en accédant au groupe de ressources associé au nom de solution que vous avez choisi.

![][img-resource-group]

Lorsque vous approvisionnez la solution préconfigurée, vous recevez un e-mail contenant un lien vers l’espace de travail Machine Learning. Vous pouvez également accéder à cet espace de travail Machine Learning à partir de la page [azureiotsuite.com][lnk-azureiotsuite] de votre solution approvisionnée lorsqu’elle est à l’état **Prêt**.

![][img-machine-learning]

Dans le portail de la solution, vous pouvez voir que l’exemple est approvisionné avec quatre appareils simulés pour représenter 2 avions comptant 2 moteurs chacun, et 4 capteurs par moteur. Lorsque vous accédez au portail de la solution pour la première fois, la simulation est arrêtée.

![][img-simulation-stopped]

Cliquez sur **Démarrer la simulation** pour commencer la simulation dans laquelle vous verrez l’historique des capteurs, la durée de vie utile restante, les cycles et l’historique de durée de vie utile restante remplir le tableau de bord.

![][img-simulation-running]

Lorsque la durée de vie utile restante est inférieure à 160 (seuil arbitraire choisi pour la démonstration), le portail de la solution affiche un symbole d’avertissement en regard de l’affichage de la durée de vie utile restante et colore le moteur d’avion dans l’image en jaune. Vous remarquerez que les valeurs de durée de vie utile restante tendent globalement à diminuer, mais avec des rebonds à la hausse ou à la baisse. Ce phénomène provient des durées de cycle et précisions de modèles variables.

![][img-simulation-warning]

La simulation complète prend environ 35 minutes pour effectuer 148 cycles. Le seuil de durée de vie utile restante de 160 est atteint pour la première fois à environ 5 minutes, et les deux moteurs atteignent le seuil à environ 8 minutes.

La simulation s’exécute sur le jeu de données complet pour les 148 cycles et se règle sur les valeurs finales de durée de vie utile restante et de cycles.

Vous pouvez arrêter la simulation à tout moment. L’option **Démarrer la simulation** réexécute la simulation à partir du début du jeu de données.

## Étapes suivantes

Maintenant que vous avez exécuté la solution préconfigurée de maintenance prédictive souhaitée, si vous souhaitez la modifier, consultez [Personnalisation des solutions préconfigurées][lnk-customize].

  
[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

<!---HONumber=AcomDC_0128_2016-->