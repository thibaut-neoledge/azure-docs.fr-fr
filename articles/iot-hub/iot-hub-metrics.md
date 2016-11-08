---
title: Métriques de diagnostic de IoT Hub
description: Vue d’ensemble des métriques Azure IoT Hub, qui permet aux utilisateurs d’évaluer l’intégrité globale de leurs ressources
services: iot-hub
documentationcenter: ''
author: nberdy
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: nberdy

---
# Introduction aux métriques de diagnostic
Les métriques de diagnostic vous offrent de meilleures données sur l’état des ressources Azure de votre abonnement. Grâce aux métriques, vous pouvez évaluer l’intégrité globale du service et des appareils connectés à ce dernier. Les statistiques accessibles à l’utilisateur sont importantes, car elles vous permettent d’effectuer le suivi de votre hub IoT et de connaître les causes des problèmes sans contacter le support Azure.

Vous pouvez activer les métriques de diagnostic à partir du portail Azure.

## Comment activer les métriques de diagnostic
1. Créez un hub IoT. Pour savoir comment créer un hub IoT, consultez le guide [Prise en main][lnk-get-started].
2. Ouvrez le panneau de votre hub IoT. De là, cliquez sur **Diagnostics**.
   
    ![][1]
3. Configurez vos diagnostics en définissant le statut sur **Activé** et en sélectionnant un compte de stockage pour stocker les données de diagnostic. Cochez **Mesures**, puis appuyez sur **Enregistrer**. Notez que le compte de stockage doit être créé à l’avance et que le stockage vous est facturé séparément. Vous pouvez également choisir d’envoyer vos données de diagnostic à un point de terminaison de concentrateurs d’événements.
   
    ![][2]
4. Une fois que vous avez configuré les diagnostics, revenez au panneau **Vue d’ensemble** du hub IoT. Les informations sur les métriques sont renseignées dans la section **Surveillance** du panneau. Si vous cliquez sur le graphique, le volet Métriques s’ouvre. Vous pouvez alors consulter les informations sur les métriques liées à votre hub IoT et modifier la sélection des métriques affichées dans le graphique. Vous pouvez également configurer des alertes en fonction des valeurs de métriques.
   
    ![][3]

## Métriques et leur utilisation
IoT Hub fournit plusieurs métriques afin de vous donner une vue d’ensemble de l’intégrité de votre hub et du nombre total d’appareils connectés à celui-ci. Vous pouvez combiner les informations de plusieurs métriques pour obtenir une image plus grande de l’état du hub IoT. Le tableau suivant décrit les métriques dont chaque hub IoT effectue le suivi et la relation de chacune avec l’état global du hub IoT.

| Mesure | Description de la métrique | Finalité de la métrique |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |Nombre de messages envoyés sur tous les appareils |Données globales sur les envois de messages |
| d2c.telemetry.ingress.success |Nombre total de messages parvenant correctement au hub |Vue d’ensemble de l’entrée des messages réussis dans le hub |
| c2d.commands.egress.complete.success |Nombre total de messages de commande effectués par l’appareil de réception sur tous les appareils |Combiné aux métriques d’abandon et de rejet, donne une vue d’ensemble du taux de réussite globale des commandes cloud-à-appareil |
| c2d.commands.egress.abandon.success |Nombre total de messages correctement abandonnés par l’appareil de réception sur tous les appareils |Met en évidence des problèmes potentiels si la fréquence d’abandon de messages est anormalement élevée |
| c2d.commands.egress.reject.success |Nombre total de messages correctement rejetés par l’appareil de réception sur tous les appareils |Met en évidence des problèmes potentiels si la fréquence de rejet de messages est anormalement élevée |
| devices.totalDevices |Valeurs moyenne, minimale et maximale du nombre d’appareils enregistrés auprès du hub IoT |Nombre d’appareils enregistrés auprès du hub |
| devices.connectedDevices.allProtocol |Valeurs moyenne, minimale et maximale du nombre d’appareils connectés simultanément |Vue d’ensemble du nombre d’appareils connectés au hub |

## Étapes suivantes
Les métriques de diagnostic n’étant plus un secret pour vous, suivez les liens ci-après pour en savoir plus sur la gestion d’Azure IoT Hub :

* [Surveillance des opérations][lnk-monitor]
* [Gérer l’accès à IoT Hub][lnk-itpro]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Conception de votre solution][lnk-design]
* [Guide du développeur][lnk-devguide]
* [Exploration de la gestion des appareils à l’aide de l’exemple d’interface utilisateur][lnk-dmui]
* [Simulation d’un appareil avec le Kit de développement logiciel (SDK) Gateway][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0817_2016-->