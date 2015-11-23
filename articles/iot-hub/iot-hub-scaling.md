<properties
 pageTitle="Mise à l’échelle Azure IoT Hub | Microsoft Azure"
 description="Décrit comment mettre à l’échelle Azure IoT Hub."
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

# Mise à l’échelle IoT Hub

IoT Hub peut prendre en charge jusqu’à un million d’appareils connectés simultanément, en augmentant le nombre d’unités IoT Hub S1 ou d’unités S2 à 2 000. Pour plus d’informations, consultez la [tarification IoT Hub][lnk-pricing].

Chaque unité IoT Hub autorise un certain nombre d’identités d’appareils pouvant être connectées simultanément dans le registre, et un nombre de messages quotidiens.

Pour mettre correctement à l’échelle votre solution, étudiez votre utilisation particulière d’IoT Hub. Prenez plus particulièrement en compte le débit maximal requis pour les catégories d’opérations suivantes :

* Messages Appareil vers cloud
* Messages Cloud vers appareil
* Opérations du registre d’identité

Outre ces informations sur le débit, consultez les [quotas et limites IoT Hub][] et concevez votre solution en conséquence.

## Débit de message Appareil vers cloud et Cloud vers appareil.

La meilleure façon de dimensionner une solution IoT Hub consiste à évaluer le trafic sur une base par appareil.

Les messages Appareil vers cloud respectent les recommandations de débit soutenu.

| Niveau | Débit soutenu | Vitesse de transmission soutenue |
| ---- | -------------------- | ------------------- |
| S1 | jusqu’à 8 Ko/h par appareil | moyenne de 4 messages/heure par appareil |
| S2 | jusqu’à 4 Ko/min par appareil | moyenne de 2 messages/min par appareil |

Lors de la réception des messages appareil vers cloud, l’application principale peut attendre le débit maximal suivant (pour tous les lecteurs).

| Niveau | Débit soutenu |
| ---- | -------------------- |
| S1 | jusqu’à 120 Ko/min par unité, avec 2 Mo/s minimum. |
| S2 | jusqu’à 4 Mo/min par unité, avec 2 Mo/s minimum. |

Échelles de performance des messages cloud vers appareil, chaque appareil recevant jusqu’à 5 messages par minute.

## Débit des opérations de registre d’identité

Les opérations de registre d’identité IoT Hub ne sont pas censées être des opérations d’exécution, car elles sont principalement liées à la configuration d’appareils.

Pour obtenir les pics de performances spécifiques, consultez [Quotas et limites IoT Hub][].

## Partitionnement

Un hub IoT unique peut recevoir des millions d’appareils, mais votre solution nécessite parfois des caractéristiques de performances spécifiques qu’un seul hub IoT ne peut pas assurer. Dans ce cas, pensez à partitionner vos appareils sur plusieurs hubs IoT, à lisser les pics de trafic et à obtenir les débits et les vitesses de fonctionnement requis.

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Azure IoT Hub :

- [Prise en main des IoT Hubs (didacticiel)][lnk-get-started].
- [Qu’est-ce qu’Azure IoT Hub ?][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[quotas et limites IoT Hub]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Qu’est-ce qu’Azure IoT Hub ?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Nov15_HO3-->