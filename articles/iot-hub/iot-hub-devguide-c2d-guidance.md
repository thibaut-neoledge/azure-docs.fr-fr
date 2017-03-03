---
title: Options cloud vers appareil Azure IoT Hub | Microsoft Docs
description: "Guide du développeur : conseils pour l’utilisation de méthodes directes, les propriétés souhaitées des jumeaux d’appareil ou les messages cloud-à-appareil pour les communications cloud à appareil."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: dea438c8b1bd3d6331f84de01def989f66b6f387
ms.openlocfilehash: 88ae905708a421052666316eb81c1dd97d6c9635
ms.lasthandoff: 02/09/2017


---
# <a name="cloud-to-device-communications-guidance"></a>Conseils pour les communications cloud-à-appareil
IoT Hub propose trois options pour les applications pour appareil afin d’exposer les fonctionnalités sur un appareil principal :

* [Méthodes directes][lnk-methods], pour les communications qui nécessitent une confirmation immédiate de leur résultat, généralement le contrôle interactif de l’appareil, par exemple, activer un ventilateur ;
* [Propriétés souhaitées des représentations][lnk-twins], pour les commandes de longue durée pour placer l’appareil dans l’état désiré. Par exemple, définissez l’intervalle d’envoi de la télémétrie sur 30 minutes ;
* [Messages cloud-à-appareil (C2D)][lnk-c2d], pour les notifications unidirectionnelles à l’application pour appareil.

Voici une comparaison détaillée des différentes options de communication cloud-à-appareil.

|  | Méthodes directes | Propriétés souhaitées des représentations | Messages C2D |
| ---- | ------- | ---------- | ---- |
| Scénario | Commandes qui nécessitent une confirmation immédiate, par exemple, activer un ventilateur. | Commandes de longue durée pour placer l’appareil dans l’état désiré. Par exemple, définissez l’intervalle d’envoi de la télémétrie sur 30 minutes. | Notifications unidirectionnelles à l’application pour appareil. |
| Flux de données | Bidirectionnel. L’application pour appareil peut répondre immédiatement à la méthode. Le dispositif principal reçoit les résultats en fonction du contexte de la demande. | Unidirectionnel. L’application pour appareil reçoit une notification avec la modification de propriété. | Unidirectionnel. L’application pour appareil reçoit le message
| Durabilité | Les appareils déconnectés ne sont pas contactés. Le serveur principal est averti que l’appareil n’est pas connecté. | Les valeurs de propriété sont conservées dans la représentation d’appareil. L’appareil les lira lors de la reconnexion suivante. Les valeurs de propriété sont récupérables avec le [langage de requête d’IoT Hub][lnk-query]. | Les messages peuvent être conservés par IoT Hub jusqu’à 48 heures. |
| Cibles | Appareil unique utilisant le paramètre **deviceId** ou appareils multiples utilisant le paramètre [jobs][lnk-jobs]. | Appareil unique utilisant le paramètre **deviceId** ou appareils multiples utilisant le paramètre [jobs][lnk-jobs]. | Appareil unique par **deviceId**. |
| Taille | Demandes jusqu’à8 Ko et réponses jusqu’à 8 Ko. | La taille maximum des propriétés souhaitées est de 8 Ko. | Messages jusqu’à 64 Ko. |
| Fréquence | Élevée. Pour plus d’informations, référez-vous à [Limites de IoT Hub][lnk-quotas]. | Moyenne. Pour plus d’informations, référez-vous à [Limites de IoT Hub][lnk-quotas]. | Faible. Pour plus d’informations, référez-vous à [Limites de IoT Hub][lnk-quotas]. |
| Protocole | Disponible actuellement uniquement lorsque vous utilisez MQTT. | Disponible actuellement uniquement lorsque vous utilisez MQTT. | Disponible sur tous les protocoles. L’appareil doit interroger lors de l’utilisation de HTTP. |

Découvrez comment utiliser les méthodes directes, les propriétés souhaitées et les messages cloud-à-appareil grâce aux didacticiels suivants :

* [Utiliser les méthodes directes][lnk-methods-tutorial], pour les méthodes directes ;
* [Utiliser des propriétés souhaitées pour configurer des appareils][lnk-twin-properties], pour les propriétés souhaitées des jumeaux d’appareil ; 
* [Envoyer des messages cloud-à-appareil][lnk-c2d-tutorial], pour les messages cloud-à-appareil.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md

