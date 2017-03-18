---
title: "Prise en main d’Azure IoT Hub | Microsoft Docs"
description: Prise en main du service IoT Hub
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d7360c3d41a0e4cf0d182e510d6bc2fccd915dd1
ms.openlocfilehash: 51ac0c0d3a91070fc8f5b3892409af838e91068e
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-azure-iot-hub-or-azure-iot-gateway-sdk"></a>Prise en main d’Azure IoT Hub ou du kit de développement logiciel (SDK) Azure IoT Gateway

Pour la prise en main du service IoT Hub ou du Kit de développement logiciel (SDK) Gateway, vous avez le choix entre plusieurs didacticiels.

## <a name="iot-hub"></a>IoT Hub

Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT (Internet des objets) et un serveur principal de solution.

Pour la prise en main du service IoT Hub, vous pouvez :

- Suivre un didacticiel qui utilise un appareil simulé en cours d’exécution sur votre ordinateur de développement. Choisir un didacticiel de prise en main qui utilise votre langage de programmation de prédilection : [.NET][lnk-dotnet], [Java][lnk-java] ou [Node.js][lnk-nodejs].

- Suivre un didacticiel qui utilise un appareil physique. Choisir un didacticiel de prise en main qui utilise votre plateforme matérielle de prédilection : [Raspberry Pi][lnk-rasp-pi], [Intel Edison][lnk-edison] ou [Arduino][lnk-arduino]. Ces didacticiels incluent des informations sur la façon d’obtenir des appareils matériels.

- Apprenez à utiliser le langage C pour développer des appareils IoT, dans l’article [Présentation du kit de développement logiciel (SDK) Azure IoT device pour C][lnk-c-intro].

## <a name="gateway-sdk"></a>Kit de développement logiciel (SDK) Gateway

Vous pouvez utiliser le Kit de développement logiciel (SDK) Gateway pour créer une passerelle de champ personnalisé. Une passerelle effectue différentes tâches comme exécuter des analyses, prendre des décisions urgentes pour réduire la latence, fournir des services de gestion des appareils, appliquer des contraintes de confidentialité et de sécurité, mais aussi effectuer la traduction de protocole.

Pour la prise en main du Kit de développement logiciel (SDK) Gateway, vous pouvez :

- Suivre un didacticiel qui utilise une passerelle simulée en cours d’exécution sur votre ordinateur de développement. Vous pouvez choisir un didacticiel de prise en main pour [Linux][lnk-linux] ou [Windows][lnk-windows].

- Suivre un didacticiel qui utilise un appareil physique. Vous pouvez choisir un didacticiel de prise en main qui utilise un [appareil simulé avec un Intel NUC (Next Unit of Computing)][lnk-gateway-sim] ou un [appareil SensorTag avec un Intel NUC][lnk-gateway-tag].

## <a name="next-steps"></a>Étapes suivantes

Après avoir pris connaissance des didacticiels de prise en main, vous pouvez explorer d’autres fonctionnalités d’IoT Hub et du Kit de développement logiciel (SDK) Gateway dans les didacticiels [Guide du développeur][lnk-devguide] et de [procédures][lnk-howto].

[lnk-dotnet]: ./iot-hub-csharp-csharp-getstarted.md
[lnk-java]: ./iot-hub-java-java-getstarted.md
[lnk-nodejs]: ./iot-hub-node-node-getstarted.md
[lnk-c-intro]: ./iot-hub-device-sdk-c-intro.md
[lnk-rasp-pi]: ./iot-hub-raspberry-pi-kit-node-get-started.md
[lnk-edison]: ./iot-hub-intel-edison-kit-node-get-started.md
[lnk-arduino]: ./iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[lnk-linux]: ./iot-hub-linux-gateway-sdk-get-started.md
[lnk-windows]: ./iot-hub-windows-gateway-sdk-get-started.md
[lnk-gateway-sim]: ./iot-hub-gateway-kit-c-sim-get-started.md
[lnk-gateway-tag]: ./iot-hub-gateway-kit-c-get-started.md
[lnk-devguide]: ./iot-hub-devguide.md
[lnk-howto]: ./iot-hub-how-to.md

