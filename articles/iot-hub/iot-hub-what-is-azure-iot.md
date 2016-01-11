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

Azure IoT Hub est un service Azure qui reçoit des mesures de télémétrie à l’échelle, en provenance de vos appareils et achemine ces données vers un processeur d’événements de flux. Vous pouvez utiliser IoT Hub pour implémenter votre propre backend de solution. IoT Hub peut également envoyer des commandes Cloud vers appareils pour des appareils spécifiques. IoT Hub inclut en outre un registre d’identités d’appareils qui vous permet de configurer des appareils et de décider quels appareils peuvent se connecter au hub. Pour plus d'informations, consultez les rubriques suivantes :

- [Qu’est-ce qu’IoT Hub ?][lnk-iot-hub]
- [Prise en main d’IoT Hub][lnk-getstarted]

Pour implémenter des applications clientes sur un large éventail de plateformes matérielles et de systèmes d’exploitation d’appareils, vous pouvez utiliser les Kits de développement logiciel (SDK) d’appareils IoT. Ces kits incluent des bibliothèques qui facilitent l’envoi de données de télémétrie vers un IoT Hub et la réception de commandes Cloud vers appareils. Avec les Kits de développement logiciel (SDK), vous avez le choix parmi un grand nombre de protocoles réseau pour communiquer avec IoT Hub. Pour plus d’informations, consultez la rubrique [plus d’informations sur les kits de développement logiciel (SDK) d’appareils][lnk-device-sdks].

[Azure IoT Suite][lnk-iot-suite] est un ensemble de solutions préconfigurées qui peut vous intéresser. IoT Suite vous permet de démarrer rapidement et de mettre à l’échelle des projets IoT pour résoudre des scénarios IoT courants tels que la surveillance à distance, la gestion des éléments multimédias et la maintenance prédictive.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: http://azure.microsoft.com/solutions/iot/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1223_2015-->