<properties
 pageTitle="Comparaison d’Azure IoT Hub et Azure Event Hubs | Microsoft Azure"
 description="Comparaison des services Azure IoT Hub et Azure Event Hubs mettant en avant les différences de fonction et des exemples d’utilisation."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Comparaison entre IoT Hub et Event Hubs

L’une des principales utilisations d’Azure IoT Hub consiste à rassembler des mesures de télémétrie relevées par des appareils. Pour cette raison, IoT Hub est souvent comparé à [Azure Event Hubs][]. Les concentrateurs d'événements Azure constituent un service de traitement des événements. Celui-ci fournit des entrées d'événements et de télémétrie vers le cloud à grande échelle, avec une faible latence et une grande fiabilité.

Toutefois, les services présentent de nombreuses différences répertoriées dans les sections qui suivent.

| Domaine | IoT Hub | Event Hubs |
| ---- | ------- | ---------- |
| Modèles de communication | Fournit une entrée d’événement appareil vers cloud et de messagerie cloud vers appareil. | Fournit uniquement une entrée d’événement (en général utilisé pour les scénarios appareil vers cloud). |
| Sécurité | Fournit une identité par appareil et le contrôle d’accès révocable. Consultez la [section Sécurité du guide du développeur IoT Hub]. | Fournit des [Stratégies d’accès partagé][Event Hub - security] à l’échelle d’Event Hubs, avec prise en charge de révocation limitée à l’aide des [stratégies de l’éditeur][Event Hub publisher policies]. Dans le cadre des solutions IoT (Internet des objets), il est souvent nécessaire de mettre en œuvre une solution personnalisée pour prendre en charge les informations d’identification par appareil et les mesures anti-usurpation d’identité. |
| Mise à l'échelle | est optimisé pour prendre en charge des millions d’appareils connectés simultanément. | peut prendre en charge un nombre de connexions simultanées moindre : jusqu’à 5 000 connexions AMQP, conformément à ce qui est défini dans [Quotas Azure Service Bus][]. En revanche, Event Hubs vous permet de spécifier une partition pour chaque message envoyé. |
| Kits de développement logiciel (SDK) d’appareil | Fournit [des Kits de développement logiciel (SDK) d’appareil][Azure IoT Hub SDKs] pour une grande variété de plateformes et de langages. | est prise en charge sur .NET, et C. Elle offre des interfaces d’envoi HTTP et AMQP. |

Pour résumer, même si le seul cas d’utilisation est l’entrée de mesures de télémétrie d’un appareil vers le cloud, IoT Hub offre un service spécialement conçu pour la connectivité d’appareil IoT. Les propositions de valeur pour ces scénarios continueront à se développer avec des fonctionnalités spécifiques à IoT. Event Hubs est conçu pour les entrées d’événements à grande échelle, dans les contextes inter et intra centres de données.

Il n’est pas rare d’utiliser à la fois IoT Hub et Event Hubs. IoT Hub gère les communications appareil vers cloud et Event Hubs assure l’entrée des événements dans des moteurs de traitement en temps réel, lors des étapes ultérieures.

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Azure IoT Hub :

- [Prise en main d’Azure IoT Hub (didacticiel)][lnk-get-started]
- [Qu’est-ce qu’Azure IoT Hub ?][]

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[section Sécurité du guide du développeur IoT Hub]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Quotas Azure Service Bus]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Qu’est-ce qu’Azure IoT Hub ?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_1223_2015-->