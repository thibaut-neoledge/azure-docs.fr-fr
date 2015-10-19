<properties
	pageTitle="Vue d’ensemble de la suite Microsoft Azure IoT | Microsoft Azure"
	description="Cette section fournit une vue d'ensemble de la suite Azure IoT."
	services=""
	documentationCenter=".net"
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="10/06/2015"
     ms.author="araguila"/>

# Vue d’ensemble de la suite Azure IoT

Microsoft propose un large éventail de fonctionnalités avec les services Azure IoT. Ces services offrent des capacités de niveau entreprise pour collecter les données des appareils, analyser les flux de données en mouvement, stocker et interroger de grands ensembles de données, visualiser des données à la fois en temps réel et sous forme d’historique, et s’intégrer à d’importants systèmes d’arrière-guichet. La suite Azure IoT regroupe un ensemble de services couramment exploités et des capacités étendues permettant aux clients d’optimiser le retour sur investissement. Ces extensions incluent des solutions préconfigurées qui offrent une implémentation basique des modèles de solutions les plus courants. En les combinant aux kits de développement logiciel (SDK) IoT, les clients peuvent facilement personnaliser des solutions préconfigurées ou les utiliser comme exemples pour le développement de nouvelles solutions.

## Services Azure IoT de la suite Azure IoT

La suite IoT Azure se situe au cœur du service IoT Hub. Ce service traite les messages de l’appareil vers le cloud et du cloud vers le service. Il agit comme la passerelle vers le cloud et les autres services clés IoT Suite.

L’analyse des données en mouvement est fournie par Azure Stream Analytics. La suite IoT tire parti de ce service pour le traitement des messages de télémétrie entrants, l’agrégation et la détection des événements. Il sert également à traiter les messages d'information qui peuvent être utilisés pour les métadonnées de l’appareil et la réponse à une commande.

Les capacités de stockage de données sont assurées par une combinaison Azure Storage et Azure Document DB. Azure Storage permet le stockage d’objets blob de télémétrie à des fins de conservation et d’analyse ultérieure. Grâce à ses capacités d’indexation de stockage des données semi-structurées, Document DB permet de gérer les métadonnées de l’appareil. Vous pouvez ainsi gérer plusieurs appareils hétérogènes en leur permettant de stocker un contenu différent.

La visualisation des données est assurée par une combinaison de sites web Azure et Microsoft Power BI. La flexibilité de Power BI permet aux clients de créer rapidement leurs propres tableaux de bord interactifs à partir des données de la suite IoT.

Vous trouverez plus de détails sur l'architecture et la façon dont ces services sont utilisés dans l'article [Microsoft Azure et l’Internet des objets (IoT)](iot-suite-what-is-azure-iot.md).

## Solutions préconfigurées

Des solutions préconfigurées sont incluses dans la suite Azure IoT pour permettre aux clients de rapidement se lancer et explorer les scénarios rendus possibles par Azure IoT Suite.

La première solution préconfigurée disponible est la [surveillance à distance](iot-suite-what-are-preconfigured-solutions.md).

<!---HONumber=Oct15_HO2-->