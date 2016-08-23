<properties
 pageTitle="Informations sur Azure IoT Hub pour les professionnels de l’informatique | Microsoft Azure"
 description="Ces informations sont destinées à aider les professionnels de l’informatique à utiliser Azure IoT Hub en ce qui concerne la configuration requise pour les ports et la sécurité."
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
 ms.date="08/09/2016"
 ms.author="dobett"/>

# Configuration et gestion de l’accès à IoT Hub

Cet article fournit des informations pour aider les professionnels de l’informatique à configurer un environnement où les appareils IoT communiquent avec IoT Hub via une infrastructure réseau.

## Connectivité réseau

Les appareils peuvent communiquer avec IoT Hub dans Azure à l’aide de divers protocoles. En règle générale, le choix du protocole dépend des exigences spécifiques de la solution. Le tableau suivant répertorie les ports de sortie qui doivent être ouverts pour qu’un appareil puisse utiliser un protocole spécifique :

| Protocole | Port(s) |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |
| AMQP sur WebSockets | 443 |
| MQTT | 8883 |
| LWM2M (gestion des appareils) | 5684 |

Lorsque vous avez créé un hub IoT dans une région Azure, le hub conserve la même adresse IP pour la durée de vie de ce hub. Toutefois, pour maintenir la qualité du service, si Microsoft déplace le hub IoT vers une autre unité d’échelle, il recevra une nouvelle adresse IP.

## IoT Hub et sécurité

Seuls les appareils inscrits avec un hub IoT sont autorisés à communiquer avec ce hub IoT. Un appareil enregistré doit disposer de l’autorisation *DeviceConnect*. Un appareil s’identifie en incluant un jeton qui encapsule l’ID unique des appareils dans chaque demande effectuée, tandis que le concentrateur vérifie que le jeton est valide et que l’appareil n’est pas sur liste noire (autorisation *DeviceConnect* révoquée). Pour plus d’informations sur les jetons pris en charge par l’IoT Hub, consultez [Utilisation des jetons de sécurité IoT Hub et des certificats X.509][lnk-tokens]

L’accès aux autres points de terminaison de gestion dans un concentrateur IoT est également contrôlé par un ensemble d’autorisations : *iothubowner*, *service*, *registryRead* et *registryReadWrite*. Une application de gestion de client qui se connecte à un hub IoT doit inclure un jeton avec les autorisations appropriées.

## Étapes suivantes

Cet article contient des informations destinées aux professionnels de l’informatique et aux développeurs configurant leurs environnements de développement et de test. La [section Sécurité du guide du développeur Azure IoT Hub][lnk-devguide] contient des informations supplémentaires sur les jetons et le système d’autorisation d’IoT Hub.

Pour explorer davantage les capacités de IoT Hub, consultez :

- [Conception de votre solution][lnk-design]
- [Guide du développeur][lnk-devguide]
- [Exploration de la gestion des appareils à l’aide de l’exemple d’interface utilisateur][lnk-dmui]
- [Simulation d’un appareil avec le Kit de développement logiciel (SDK) Gateway][lnk-gateway]

[lnk-devguide]: iot-hub-devguide.md#security

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-tokens]: iot-hub-sas-tokens.md

<!---HONumber=AcomDC_0810_2016-->