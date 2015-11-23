<properties
 pageTitle="Solutions Azure pour l’Internet des objets | Microsoft Azure"
 description="Une vue d’ensemble d’IoT sur Azure, avec un exemple d’architecture de solution et sa relation avec Azure IoT Hub, les Kits de développement logiciel et les solutions préconfigurées."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/05/2015"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-et-iot](../../includes/iot-azure-and-iot.md)]

## Étapes suivantes

Vous pouvez utiliser IoT Hub, un service Azure qui reçoit des données de télémétrie à l’échelle de vos appareils et achemine ces données vers un processeur d’événements de flux de données pour implémenter votre propre serveur principal de solution. IoT Hub peut également envoyer des commandes de cloud à appareils pour des appareils spécifiques. IoT Hub inclut en outre un registre d’identités d’appareils qui vous permet de configurer des appareils et de décider quels appareils peuvent se connecter à un hub. Pour plus d'informations, consultez les rubriques suivantes :

- [Qu’est-ce qu’IoT Hub ?][lnk-iot-hub]
- [Prise en main d’IoT Hub][lnk-getstarted]

Pour implémenter des applications clientes sur un large éventail de plateformes matérielles et de systèmes d’exploitation d’appareils, vous pouvez utiliser les Kits de développement logiciel (SDK) d’appareils IoT. Ces Kits incluent des bibliothèques qui facilitent l’envoi de données de télémétrie vers un IoT Hub et la réception de commandes de cloud à appareils. Avec les Kits de développement logiciel (SDK), vous pouvez choisir un certain nombre de protocoles réseau pour communiquer avec IoT Hub. Pour plus d'informations, consultez [Que sont les Kits de développement logiciel (SDK) d’appareil ?][lnk-device-sdks].

Vous pouvez également être intéressé par [Azure IoT Suite][lnk-iot-suite], un ensemble de solutions préconfigurées qui vous permettent de démarrer rapidement et de mettre à l’échelle des projets IoT pour résoudre des scénarios IoT courants tels que la surveillance à distance, la gestion des ressources et la maintenance prédictive.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=Nov15_HO3-->