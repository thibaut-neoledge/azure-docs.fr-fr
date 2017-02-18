---
title: "Utiliser des métriques pour surveiller Azure IoT Hub | Microsoft Docs"
description: "Guide d’utilisation des métriques Azure IoT Hub pour évaluer et surveiller l’intégrité globale de votre IoT Hub."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e8cac4af4b971320429cc4c76b8d806e314e1143


---
# <a name="iot-hub-metrics"></a>Métriques d’IoT Hub
Les métriques IoT Hub vous offrent de meilleures données sur l’état des ressources Azure IoT de votre abonnement Azure. Grâce aux métriques d’IoT Hub, vous pouvez évaluer l’intégrité globale du service et des appareils connectés à ce dernier. Les statistiques accessibles à l’utilisateur sont importantes, car elles vous permettent d’effectuer le suivi de votre hub IoT et de connaître les causes des problèmes sans contacter le support Azure.

Les métriques sont activées par défaut. Vous pouvez afficher les métriques de l’IoT Hub à partir du portail Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Affichage des métriques de l’IoT Hub
1. Créez un hub IoT. Pour savoir comment créer un IoT Hub, consultez le guide [Prise en main][lnk-get-started].
2. Ouvrez le panneau de votre hub IoT. Ici, cliquez sur **Métriques**.
   
    ![][1]
3. Dans le panneau de métriques, vous pouvez afficher les métriques de l’IoT Hub et créer des vues personnalisées de ces dernières. Vous pouvez choisir d’envoyer vos données métriques vers un point de terminaison Event Hubs ou un compte de stockage Azure en cliquant sur **Paramètres de diagnostic**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métriques IoT Hub et instructions d’utilisation
IoT Hub fournit plusieurs métriques afin de vous donner une vue d’ensemble de l’intégrité de votre hub et du nombre total d’appareils connectés. Vous pouvez combiner les informations de plusieurs métriques pour obtenir une image plus grande de l’état du hub IoT. Le tableau suivant décrit les métriques dont chaque hub IoT effectue le suivi et la relation de chacune avec l’état global du hub IoT.

| Mesure | Description de la métrique | Finalité de la métrique |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol | Nombre de messages envoyés sur tous les appareils | Données globales sur les envois de messages |
| d2c.telemetry.ingress.success | Nombre total de messages parvenant correctement au hub | Vue d’ensemble de l’entrée des messages réussis dans le hub |
| d2c.telemetry.egress.success | Nombre d’écritures réussies sur un point de terminaison | Vue d’ensemble de la distribution ramifiée des messages basée sur les itinéraires de l’utilisateur |
| d2c.telemetry.egress.invalid | Nombre de messages non remis en raison d’une incompatibilité avec le point de terminaison | Vue d’ensemble des échecs écrivant dans le jeu de points de terminaison de l’utilisateur. Des valeurs élevées peuvent indiquer des points de terminaison incorrectement configurés. |
| d2c.telemetry.egress.dropped | Nombre de messages ignorés car un point de terminaison était défectueux | Vue d’ensemble du nombre de messages supprimés en fonction de la configuration actuelle de l’IoT Hub |
| d2c.telemetry.egress.fallback | Nombre de messages correspondant à l’itinéraire de secours | Pour les utilisateurs qui dirigent tous les messages vers les points de terminaison autres que le point de terminaison prédéfini, cette métrique affiche les écarts dans la configuration de routage |
| d2c.telemetry.egress.orphaned | Nombre de messages ne correspondant à aucun itinéraire, itinéraire de secours compris | Vue d’ensemble du nombre de messages orphelins en fonction de la configuration actuelle de l’IoT Hub |
| d2c.endpoints.latency.eventHubs | Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison Event Hub, en millisecondes | La répartition permet aux utilisateurs d’identifier une mauvaise configuration du point de terminaison |
| d2c.endpoints.latency.serviceBusQueues | Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de file d’attente Service Bus, en millisecondes | La répartition permet aux utilisateurs d’identifier une mauvaise configuration du point de terminaison |
| d2c.endpoints.latency.serviceBusTopic | Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de rubrique Service Bus, en millisecondes | La répartition permet aux utilisateurs d’identifier une mauvaise configuration du point de terminaison |
| d2c.endpoints.latency.builtIn.events | Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison prédéfini (messages/événements), en millisecondes | La répartition permet aux utilisateurs d’identifier une mauvaise configuration du point de terminaison |
| c2d.commands.egress.complete.success | Nombre total de messages de commande effectués par l’appareil de réception sur tous les appareils |Combiné aux métriques d’abandon et de rejet, donne une vue d’ensemble du taux de réussite global des commandes cloud-à-appareil |
| c2d.commands.egress.abandon.success | Nombre total de messages correctement abandonnés par l’appareil de réception sur tous les appareils |Met en évidence des problèmes potentiels si la fréquence d’abandon de messages est anormalement élevée |
| c2d.commands.egress.reject.success | Nombre total de messages correctement rejetés par l’appareil de réception sur tous les appareils |Met en évidence des problèmes potentiels si la fréquence de rejet de messages est anormalement élevée |
| devices.totalDevices | Nombre d’appareils enregistrés auprès de l’IoT Hub |Nombre d’appareils enregistrés auprès du hub |
| devices.connectedDevices.allProtocol | Nombre d’appareils connectés simultanément |Vue d’ensemble du nombre d’appareils connectés au hub |

## <a name="next-steps"></a>Étapes suivantes
Les métriques d’IoT Hub n’étant plus un secret pour vous, suivez le lien ci-après pour en savoir plus sur la gestion d’Azure IoT Hub :

* [Surveillance des opérations][lnk-monitor]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Simulation d’un appareil avec le Kit de développement logiciel (SDK) de la passerelle IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Jan17_HO4-->


