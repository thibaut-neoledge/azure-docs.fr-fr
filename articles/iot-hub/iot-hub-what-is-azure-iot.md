<properties
 pageTitle="Microsoft Azure IoT Hub et l’Internet des objets (IoT) | Microsoft Azure"
 description="Une vue d’ensemble de IoT sur Azure, y compris un exemple d’architecture de solution et sa relation avec Azure IoT Hub, les Kits de développement logiciel et les solutions préconfigurées."
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-et-iot](../../includes/iot-azure-and-iot.md)]

## Étendue de cette documentation

Ces articles sur Azure et IoT se concentrent sur deux ensembles de ressources qui peuvent vous aider à implémenter votre propre solution IoT basée sur la plateforme Microsoft IoT.

- Azure IoT Hub
- Kits de développement logiciel (SDK) d’appareils Azure IoT

Vous pouvez également être intéressé par [Azure IoT Suite][lnk-iot-suite], un ensemble de solutions préconfigurées qui vous permettent de démarrer rapidement et de mettre à l’échelle des projets IoT pour résoudre des scénarios IoT courants tels que la surveillance à distance, la gestion des actifs et la maintenance prédictive.

### Azure IoT Hub

IoT Hub est un service Azure qui vous permet de recevoir des données d’appareil à cloud à l’échelle de vos appareils et d’acheminer ces données vers un processeur d’événements de flux de données. IoT Hub peut également envoyer des commandes de cloud à appareils pour des appareils spécifiques à l’aide de files d’attente spécifiques.

Le service IoT Hub inclut également un registre d’identités d’appareils qui vous permet de configurer des appareils et de décider quels appareils peuvent se connecter à un concentrateur IoT.

### Kits de développement logiciel (SDK) d’appareils Azure IoT

Microsoft fournit des Kits de développement logiciel (SDK) IoT qui vous permettent d’implémenter des applications client s’exécutant sur un large éventail de plateformes matérielles et de systèmes d’exploitation d’appareils. Le Kits de développement logiciel (SDK) IoT incluent des bibliothèques qui facilitent l’envoi de données de télémétrie vers IoT Hub et la réception de commandes de cloud à appareils vers IoT Hub. Ces Kits de développement logiciel (SDK) vous permettent de choisir un certain nombre de protocoles réseau différents pour communiquer avec Azure IoT Hub.

## Étapes suivantes

Pour la prise en main d’IoT sur Azure, explorez les ressources suivantes :

- [Prise en main d’IoT Hub][lnk-getstarted].
- [Azure IoT Hub][lnk-iot-hub].
- [Azure IoT Suite][lnk-iot-suite].  


[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/

<!---HONumber=Oct15_HO2-->