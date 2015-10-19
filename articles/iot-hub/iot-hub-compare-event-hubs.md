<properties
 pageTitle="Comparaison entre Azure IoT Hub et Event Hubs | Microsoft Azure"
 description="Comparaison entre IoT Hub et Event Hubs."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Comparaison entre IoT Hub et Event Hubs

L’une des principales utilisations d’Azure IoT Hub consiste à rassembler des mesures de télémétrie relevées par des appareils. C’est pour cette raison qu’IoT Hub est souvent comparé à [Event Hubs][], qui est un service de traitement d’événement qui permet de créer des entrées d’événement et de télémétrie sur le cloud à grande échelle, avec une faible latence et une grande fiabilité.

Les services, cependant, présentent de nombreuses différences répertoriées dans les sections qui suivent.

| Domaine | IoT Hub | Event Hubs |
| ---- | ------- | ---------- |
| Modèles de communication | Entrée d’événement appareil vers cloud et messagerie cloud vers appareil. | Entrée d’événement seulement (en général utilisé pour les scénarios appareil vers cloud). |
| Sécurité | Identité par appareil et contrôle d’accès révocable. Consultez [Guide du développeur IoT Hub - Sécurité]. | [Stratégies d’accès partagé][Event Hub - security] à l’échelle du hub d’événements avec prise en charge de révocation limitée à l’aide des [stratégies de l’éditeur][Event Hub publisher policies]. Dans le cadre des solutions IoT, il est souvent nécessaire de mettre en œuvre une solution personnalisée pour prendre en charge les informations d’identification par appareil et les mesures anti-usurpation d’identité. |
| Mise à l'échelle | IoT Hub est optimisé pour prendre en charge des millions d’appareils connectés simultanément. | Les hubs d’événements peuvent prendre en charge un nombre de connexions simultanées moindre : jusqu’à 5 000 connexions AMQP, conformément à ce qui est défini dans [Quotas Service Bus][]. En revanche, Event Hubs vous permet de spécifier une partition pour chaque message envoyé. |
| Kits de développement logiciel (SDK) d’appareil | IoT Hub fournit [des Kits de développement logiciel (SDK) d’appareil][Azure IoT Hub SDKs] pour une grande variété de plateformes et de langages. | L’option Event hubs est prise en charge sur .NET, C et offre des interfaces d’envoi HTTP et AMQP. |

En résumé, même si le seul cas d’utilisation est l’entrée de mesures de télémétrie d’un appareil vers le cloud, IoT Hub offre un service spécialement conçu pour la connectivité d’appareil IoT et va continuer de développer les propositions de valeur de ces scénarios grâce à des fonctionnalités IoT spécifiques. Event Hubs est conçu pour saisir des événements à grande échelle, dans les contextes inter et intra centres de données.

Il n’est pas rare d’utiliser à la fois IoT Hub et Event Hubs. Le premier gère les communications appareil vers cloud, et le second assure la saisie des événements dans des moteurs de traitement en temps réel.

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Azure IoT Hub :

- [Prise en main des IoT Hubs (didacticiel)][lnk-get-started].
- [Qu’est-ce qu’Azure IoT Hub ?][]

[Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Guide du développeur IoT Hub - Sécurité]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Quotas Service Bus]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Qu’est-ce qu’Azure IoT Hub ?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO2-->