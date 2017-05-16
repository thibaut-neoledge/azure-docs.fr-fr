---
title: "Azure IoT Hub : mise en route de la connexion des appareils IoT au cloud | Microsoft Docs"
description: "Découvrez comment connecter vos appareils IoT à Azure IoT Hub. Vos appareils peuvent envoyer la télémétrie à IoT Hub et Iot Hub peut surveiller et gérer vos appareils."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: Tutoriel Azure IoT Hub
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: aeb0b665b8295bba30d8c6c47cc88e446693c91f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/01/2017


---
# <a name="azure-iot-hub-get-started-tutorials"></a>Tutoriels de prise en main d’Azure IoT Hub

Vous pouvez utiliser Azure IoT Hub et les Kits de développement logiciel (SDK) d’appareil Azure IoT pour créer des solutions Internet des objets (IoT).

* Azure IoT Hub est un service entièrement géré dans le cloud qui connecte, surveille et gère vos appareils IoT en toute sécurité. Utilisez les Kits de développement (SDK) d’appareil Azure IoT pour implémenter vos appareils IoT.
* Utilisez une passerelle IoT dans les scénarios IoT plus complexes où vous devez tenir compte de facteurs tels que les appareils hérités, les coûts de bande passante, les stratégies de sécurité et de confidentialité ou le traitement des données de périmètre. Dans ces scénarios, vous utilisez le Kit de développement logiciel (SDK) de passerelle Azure IoT pour créer un appareil de passerelle qui connecte des appareils à votre IoT Hub.

## <a name="what-do-the-tutorials-cover"></a>Ce qui est couvert par les tutoriels

Ces tutoriels présentent Azure IoT Hub et les Kits de développement logiciel (SDK) d’appareil. Les tutoriels abordent des scénarios IoT courants pour faire la démonstration des fonctionnalités de IoT Hub. Ils illustrent également la manière dont IoT Hub peut être combiné avec d’autres services et outils Azure pour créer des solutions IoT plus puissantes. Dans les tutoriels, vous pouvez choisir d’utiliser des appareils IoT simulés ou réels. En outre, vous pouvez apprendre à utiliser une passerelle pour permettre aux appareils de se connecter à votre IoT Hub.

## <a name="device-setup-scenario-connect-iot-device-or-gateway-to-azure-iot-hub"></a>Scénario de configuration d’appareil : connecter un appareil ou une passerelle IoT à Azure IoT Hub

Vous pouvez choisir votre appareil réel ou simulé pour commencer.

| Appareil IoT                       | Langage de programmation |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |
| Appareil simulé                | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth]              |

En outre, vous pouvez utiliser une passerelle pour permettre aux appareils de se connecter à votre IoT Hub.

| Appareil de passerelle               | Langage de programmation | Plateforme         |
|------------------------------|----------------------|------------------|
| Intel NUC (modèle DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |
| Passerelle simulée            | C                    | [Linux][Sim_Lnx], [Windows][Sim_Win] |

## <a name="extended-iot-scenarios-use-other-azure-services-and-tools"></a>Scénarios IoT étendus : utilisation d’autres services et outils Azure

Lorsque vous avez connecté votre appareil à IoT Hub, vous pouvez explorer des scénarios supplémentaires qui utilisent d’autres services et outils Azure :

| Scénario                                    | Service ou outil Azure              |
|---------------------------------------------|------------------------------------|
| [Gérer les messages IoT Hub][Mg_IoT_Hub_Msg]                    | Outil iothub-explorer               |
| [Gérer votre appareil IoT][Mg_IoT_Dv]               | Outil iothub-explorer               |
| [Enregistrer les messages IoT Hub dans le stockage Azure][Sv_IoT_Msg_Stor]                      | Stockage de table Azure               |
| [Visualiser les données de capteur][Vis_Data]             | Microsoft Power BI, Azure Web Apps |
| [Prévisions météorologiques avec des données de capteur][Weather_Forecast] | Azure Machine Learning             |
| [Détection d’anomalie et réaction automatiques][Anomaly_Detect]    | Azure Logic Apps                   |

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous avez terminé ces tutoriels, vous pouvez explorer davantage les capacités de IoT Hub dans le [guide du développeur][lnk-dev-guide]. D’autres tutoriels sont disponibles dans la section [Procédure][lnk-how-to].


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-gateway-sdk-get-started.md
[Sim_Win]: iot-hub-windows-gateway-sdk-get-started.md
[Mg_IoT_Hub_Msg]: iot-hub-explorer-cloud-device-messaging.md
[Mg_IoT_Dv]: iot-hub-device-management-iothub-explorer.md
[Sv_IoT_Msg_Stor]: iot-hub-store-data-in-azure-table-storage.md
[Vis_Data]: iot-hub-live-data-visualization-in-power-bi.md
[Weather_Forecast]: iot-hub-weather-forecast-machine-learning.md
[Anomaly_Detect]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-dev-guide]: iot-hub-devguide.md
[lnk-how-to]: iot-hub-how-to.md
