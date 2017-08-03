---
title: "Mise en route de la connexion des appareils physiques à Azure IoT Hub | Microsoft Docs"
description: "Découvrez comment connecter des cartes et des appareils physiques à Azure IoT Hub. Vos appareils peuvent envoyer des données de télémétrie à IoT Hub, et IoT Hub peut surveiller et gérer vos appareils."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: Tutoriel Azure IoT Hub
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: c42c1c115fac70920b08d65fc1a2cdc8497f5080
ms.contentlocale: fr-fr
ms.lasthandoff: 08/01/2017

---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Didacticiels de prise en main pour les appareils physiques connectés à Azure IoT Hub

Ces tutoriels présentent Azure IoT Hub et les Kits de développement logiciel (SDK) d’appareil. Les tutoriels abordent des scénarios IoT courants pour faire la démonstration des fonctionnalités de IoT Hub. Ils illustrent également la manière dont IoT Hub peut être combiné avec d’autres services et outils Azure pour créer des solutions IoT plus puissantes. Les didacticiels répertoriés dans le tableau ci-dessous vous montrent comment créer des appareils IoT physiques.

| Appareil IoT                       | Langage de programmation |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C], [Python][Pi_Py]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |

En outre, vous pouvez utiliser une passerelle IoT Edge pour permettre aux appareils de se connecter à votre IoT Hub.

| Appareil de passerelle               | Langage de programmation | Plateforme         |
|------------------------------|----------------------|------------------|
| Intel NUC (modèle DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

