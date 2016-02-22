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
 ms.date="02/03/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-et-iot](../../includes/iot-azure-and-iot.md)]

## Étapes suivantes

Azure IoT Hub est un service Azure qui autorise des communications bidirectionnelles sécurisées et fiables entre votre serveur d’applications principal et des millions d’appareils. Il permet au serveur d’applications principal de recevoir des données de télémétrie à grande échelle de vos appareils, d’acheminer ces données vers un processeur d’événements de flux et d’envoyer des commandes cloud-à-appareil à des appareils spécifiques. Vous pouvez utiliser IoT Hub pour implémenter votre propre serveur principal de solution. IoT Hub offre aussi un registre d’identités d’appareils qui permet de configurer des appareils, leurs informations d’identification et leurs droits de connexion au hub. Pour plus d'informations, consultez les rubriques suivantes :

- [Qu’est-ce qu’IoT Hub ?][lnk-iot-hub]
- [Prise en main d’IoT Hub][lnk-getstarted]

Pour implémenter des applications clientes sur un large éventail de plateformes matérielles et de systèmes d’exploitation d’appareils, vous pouvez utiliser les Kits de développement logiciel (SDK) d’appareils IoT. Ces kits incluent des bibliothèques qui facilitent l’envoi de données de télémétrie vers un IoT Hub et la réception de commandes cloud-à-appareils. Avec les Kits de développement logiciel (SDK), vous avez le choix parmi un grand nombre de protocoles réseau pour communiquer avec IoT Hub. Pour plus d’informations, consultez la rubrique [plus d’informations sur les kits de développement logiciel (SDK) d’appareils][lnk-device-sdks].

[Azure IoT Suite][lnk-iot-suite] est un ensemble de solutions préconfigurées qui peut vous intéresser. IoT Suite vous permet de démarrer rapidement et de mettre à l’échelle des projets IoT pour résoudre des scénarios IoT courants tels que la surveillance à distance, la gestion des éléments multimédias et la maintenance prédictive.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0211_2016-->