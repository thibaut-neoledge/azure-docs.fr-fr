---
title: Passerelle de protocole IoT Azure | Microsoft Docs
description: "Comment utiliser une passerelle de protocole Azure IoT pour étendre les capacité IoT Hub et la prise en charge du protocole pour autoriser les appareils à se connecter à votre hub ç l’aide des protocoles non pris en charge par IoT Hub en mode natif."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 555e59ae-3136-4533-8ba8-f3a3b6acf648
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: elioda
ms.openlocfilehash: 1ed8ec28b95bbc91b731fd7bb7b3f1f6654e7fcf
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2017
---
# Prise en charge de protocoles supplémentaires pour IoT Hub
Azure IoT Hub en mode natif prend en charge la communication par le biais des protocoles AMQP, MQTT et HTTPS. Dans certains cas, il est possible que des appareils ou des passerelles ne soient pas en mesure d’utiliser l’un des protocoles standard et nécessitent une adaptation de protocole. Dans ce cas, vous pouvez utiliser une passerelle personnalisée. Une passerelle personnalisée active l’adaptation de protocole pour les points de terminaison IoT Hub en effectuant le pontage du trafic vers et à partir d’IoT Hub. Vous pouvez utiliser la [passerelle de protocole Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) comme passerelle personnalisée pour permettre l’adaptation de protocole pour IoT Hub.

## passerelle de protocole Azure IoT
La passerelle de protocole Azure IoT est une infrastructure pour l’adaptation du protocole, conçue pour la communication bidirectionnelle à grande échelle entre les appareils, avec IoT Hub. La passerelle de protocole est un composant direct qui accepte les connexions des appareils via un protocole spécifique. Elle réduit le trafic vers IoT Hub via AMQP 1.0. 

Vous pouvez déployer la passerelle de protocole dans Azure de manière très évolutive grâce à Azure Service Fabric, aux rôles de travail d’Azure Cloud Services ou aux machines virtuelles Windows. En outre, la passerelle de protocole peut être déployée dans les environnements locaux comme des passerelles de champ.

La passerelle de protocole Azure IoT inclut un adaptateur de protocole MQTT grâce auquel vous pouvez personnaliser le comportement du protocole MQTT, si nécessaire. Étant donné qu’IoT Hub prend naturellement en charge le protocole MQTT v3.1.1, vous ne devez envisager l’utilisation de l’adaptateur de protocole MQTT que si des personnalisations de protocole ou de fonctionnalités supplémentaires sont requises.

L’adaptateur MQTT illustre également le modèle de programmation pour la création d’adaptateurs de protocole pour d’autres protocoles. En outre, le modèle de programmation de passerelle de protocole Azure IoT vous permet de connecter des composants personnalisés pour les traitements spécialisés, comme l’authentification personnalisée, les transformations de messages, la compression / décompression ou le chiffrement / déchiffrement du trafic entre les appareils et IoT Hub.

Pour plus de flexibilité, la passerelle de protocole Azure IoT et l’implémentation de MQTT sont fournies dans un projet logiciel open source. Vous pouvez utiliser le projet open source pour ajouter la prise en charge de différents protocoles et versions de protocole, ou personnaliser l’implémentation en fonction de votre scénario. 

## Étapes suivantes
Pour en savoir plus sur la passerelle de protocole Azure IoT, son utilisation et son déploiement dans le cadre de votre solution IoT, consultez :

* [Référentiel sur la passerelle de protocole Azure IoT sur GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guide du développeur de passerelle de protocole Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Pour en savoir plus sur la planification de votre déploiement IoT Hub, consultez :

* [Comparer à Event Hubs][lnk-compare]
* [Mise à l’échelle, haute disponibilité et récupération d’urgence][lnk-scaling]
* [Guide du développeur IoT Hub][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
