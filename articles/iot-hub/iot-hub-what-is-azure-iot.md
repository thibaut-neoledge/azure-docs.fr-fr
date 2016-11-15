---
title: "Solutions Azure pour l’Internet des objets (IoT)| Microsoft Docs"
description: "Une vue d’ensemble d’IoT sur Azure, avec un exemple d’architecture de solution et sa relation avec Azure IoT Hub, les Kits de développement logiciel et les solutions préconfigurées."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2dbf639abfa505eb329769bcc346efb5f1db443e


---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Étapes suivantes
Azure IoT Hub est un service Azure qui autorise des communications bidirectionnelles sécurisées et fiables entre votre serveur d’applications principal et des millions d’appareils. Grâce à ce service, le serveur d’application principal peut :

* Recevoir des données de télémétrie à grande échelle en provenance de vos appareils
* Acheminer les données vers un processeur d’événements de flux
* Recevoir des fichiers chargés par les appareils
* Envoyer des commandes cloud-à-appareil à des appareils spécifiques

Vous pouvez utiliser IoT Hub pour implémenter votre propre serveur principal de solution. IoT Hub offre aussi un registre d’identités d’appareils qui permet de configurer des appareils, leurs informations d’identification et leurs droits de connexion au hub. Pour en savoir plus sur IoT Hub, consultez [Qu’est-ce qu’IoT Hub ?][lnk-iot-hub].

Pour découvrir comment Azure IoT Hub permet une gestion des appareils basée sur des normes pour gérer, configurer et mettre à jour à distance vos appareils, consultez [Vue d’ensemble de la gestion des appareils avec IoT Hub][lnk-device-management].

Pour implémenter des applications clientes sur un large éventail de plateformes matérielles et de systèmes d’exploitation d’appareils, vous pouvez utiliser les Kits de développement logiciel (SDK) d’appareils IoT. Ces kits incluent des bibliothèques qui facilitent l’envoi de données de télémétrie vers un IoT Hub et la réception de commandes cloud-à-appareils. Avec les Kits de développement logiciel (SDK), vous avez le choix parmi plusieurs protocoles réseau pour communiquer avec IoT Hub. Pour plus d’informations, consultez la rubrique [plus d’informations sur les kits de développement logiciel (SDK) d’appareils][lnk-device-sdks].

Pour bien démarrer avec l’écriture du code et l’exécution de certains exemples, consultez le didacticiel [Prise en main d’IoT Hub][lnk-get-started].

[Azure IoT Suite][lnk-iot-suite] est un ensemble de solutions préconfigurées qui peut vous intéresser. IoT Suite vous permet de démarrer rapidement et de mettre à l’échelle des projets IoT pour résoudre des scénarios IoT courants tels que la surveillance à distance, la gestion des éléments multimédias et la maintenance prédictive.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Nov16_HO2-->


