<properties
	pageTitle="Vue d’ensemble de la suite Microsoft Azure IoT | Microsoft Azure"
	description="Ceci offre une vue d’ensemble d’Azure IoT Suite, notamment l’empaquetage et les solutions préconfigurées."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/27/2015"
     ms.author="dobett"/>

# Vue d’ensemble de la suite Azure IoT

Les services Azure IoT offrent un large éventail de fonctionnalités. Ces services de niveau professionnel vous permettent d’effectuer les tâches suivantes :

- Collecter des données à partir des appareils
- Analyser les flux de données en mouvement
- Stocker et interroger des jeux de données volumineux
- Visualiser les données historiques et en temps réel
- Intégrer des systèmes back-office

Azure IoT Suite regroupe des services Azure couramment exploités avec des extensions personnalisées en tant que solutions préconfigurées. Ces solutions préconfigurées sont des implémentations de base des modèles de solution IoT courants qui vous aident à réduire le temps dont vous avez besoin pour fournir vos solutions IoT. À l’aide des [kits de développement logiciel IoT][lnk-sdks], vous pouvez facilement personnaliser ces solutions préconfigurées pour répondre à vos besoins, ou bien les utiliser comme des exemples lorsque vous développez des solutions.

La vidéo suivante offre une présentation d’Azure IoT Suite :

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Services Azure IoT de la suite Azure IoT

Azure IoT Suite se situe au cœur du service [Azure IoT Hub][lnk-iot-hub]. Ce service fournit les fonctionnalités de messagerie Appareil vers cloud et Cloud vers appareil, et agit comme la passerelle vers le cloud et les autres services clés IoT Suite.

[Azure Stream Analytics][lnk-asa] fournit l’analyse des données en mouvement. IoT Suite tire parti de ce service pour traiter la télémétrie entrante, effectuer l’agrégation et détecter les événements. Les solutions préconfigurées utilisent également Stream Analytics pour traiter les messages d’information qui contiennent des données telles que les réponses de métadonnées ou de commandes provenant des appareils.

[Azure Storage][lnk-azure-storage] et [Azure DocumentDB][lnk-document-db] fournissent les capacités de stockage de données. Les solutions préconfigurées utilisent Blob Storage pour stocker les données de télémétrie et les rendre disponibles pour l’analyse. Les solutions préconfigurées utilisent le stockage indexé sur une fonctionnalité de données semi-structurées de DocumentDB pour gérer les métadonnées de l’appareil. Ainsi, les solutions sont en mesure de gérer des appareils hétérogènes présentant des besoins de stockage de contenu différents.

[Azure Web Apps][lnk-web-apps] et [Microsoft Power BI][lnk-power-bi] fournissent des fonctionnalités de visualisation des données. La flexibilité de Power BI vous permet de créer rapidement vos propres tableaux de bord interactifs utilisant des données IoT Suite.

Pour une vue d’ensemble de l’architecture d’une solution IoT standard, consultez [Microsoft Azure et l’Internet des objets (IoT)][iot-suite-what-is-azure-iot].

## Solutions préconfigurées

IoT Suite comprend des solutions préconfigurées qui vous permettent une prise en main et une exploration rapides des scénarios IoT courants permis par Azure IoT Suite. Vous pouvez déployer des solutions préconfigurées sur votre abonnement Azure, puis exécuter une solution IoT complète, de bout en bout.

## Étapes suivantes

Pour en savoir plus sur les solutions préconfigurées dans IoT Suite, consultez [Que sont les solutions préconfigurées Azure IoT ?][lnk-what-are-preconfig]

Pour commencer à utiliser l’une des solutions préconfigurées, consultez [Prise en main des solutions préconfigurées IoT][lnk-preconfig-start].

Pour en savoir plus sur le service Azure IoT Hub, consultez la [documentation IoT Hub][lnk-iot-hub].


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!---HONumber=AcomDC_0128_2016-->