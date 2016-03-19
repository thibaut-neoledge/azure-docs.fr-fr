<properties
 pageTitle="Métriques de diagnostic IoT Hub"
 description="Vue d’ensemble des métriques Azure IoT Hub, qui permet aux utilisateurs d’évaluer l’intégrité globale de leurs ressources"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="nberdy"/>

# Introduction aux métriques de diagnostic

Les métriques de diagnostic s’inscrivent dans un effort de proposer des métriques Azure pour que vous disposiez de meilleures données sur l’état des ressources Azure de votre abonnement. Grâce aux métriques, un utilisateur peut évaluer l’intégrité globale du service et des appareils connectés à ce dernier. Les statistiques accessibles à l’utilisateur sont importantes, car elles lui permettent d’effectuer le suivi de son hub IoT et de connaître les causes des problèmes sans contacter le support Azure.

Vous pouvez activer les métriques de diagnostic à partir du portail Azure.

## Comment activer les métriques de diagnostic

1. Créez un hub IoT. Pour savoir comment créer un hub IoT, consultez le guide [Prise en main][lnk-get-started].

2. Ouvrez le panneau de votre hub IoT. Cliquez sur **Tous les paramètres**, puis cliquez sur **Diagnostics**.

    ![][1]

3. Configurez vos diagnostics en définissant le statut sur **Activé** et en sélectionnant un compte de stockage pour stocker les données de diagnostic. Cochez la case Métriques, puis appuyez sur **Enregistrer**. Notez que le compte de stockage doit être créé à l’avance et que le stockage vous est facturé séparément.

    ![][2]

4. Une fois que vous avez configuré les diagnostics, revenez au panneau principal du hub IoT. Les informations sur les métriques sont renseignées dans la section **Surveillance** du panneau. Si vous cliquez sur le graphique, le volet Métriques s’ouvre, où vous pouvez consulter les informations sur les métriques liées à votre hub IoT et modifier la sélection des métriques affichées dans le graphique. Vous pouvez également configurer des alertes en fonction des valeurs de métriques.

    ![][3]

## Métriques et leur utilisation

IoT Hub fournit plusieurs métriques afin de vous donner une vue d’ensemble de l’intégrité de votre hub et du nombre total d’appareils connectés à celui-ci. Vous pouvez combiner les informations de plusieurs métriques pour obtenir une image plus grande de l’état du hub IoT. Le tableau suivant décrit les métriques dont chaque hub IoT effectue le suivi et la relation de chacune avec l’état global du hub IoT.

| Mesure | Description de la métrique | Finalité de la métrique |
| ---- | ---- | ---- |
| d2c.telemetry.ingress.allProtocol | Nombre de messages envoyés sur tous les appareils | Données globales sur les envois de messages |
| d2c.telemetry.ingress.success | Nombre total de messages parvenant correctement au hub | Vue d’ensemble de l’entrée des messages réussis dans le hub |
| c2d.commands.egress.complete.success | Nombre total de messages de commande effectués par l’appareil de réception sur tous les appareils | Combiné aux métriques d’abandon et de rejet, donne une vue d’ensemble du taux de réussite globale des commandes cloud-à-appareil |
| c2d.commands.egress.abandon.success | Nombre total de messages correctement abandonnés par l’appareil de réception sur tous les appareils | Met en évidence des problèmes potentiels si la fréquence d’abandon de messages est anormalement élevée |
| c2d.commands.egress.reject.success | Nombre total de messages correctement rejetés par l’appareil de réception sur tous les appareils | Met en évidence des problèmes potentiels si la fréquence de rejet de messages est anormalement élevée |
| devices.totalDevices | Valeurs moyenne, minimale et maximale du nombre d’appareils enregistrés auprès du hub IoT | Nombre d’appareils enregistrés auprès du hub |
| devices.connectedDevices.allProtocol | Valeurs moyenne, minimale et maximale du nombre d’appareils connectés simultanément | Vue d’ensemble du nombre d’appareils connectés au hub |

## Étapes suivantes

Les métriques de diagnostic n’étant plus un secret pour vous, suivez les liens ci-après pour en savoir plus :

- [Surveillance des opérations IoT Hub][lnk-operations-monitoring]
- [Mise à l’échelle IoT Hub][lnk-scaling]
- [Haute disponibilité et récupération d’urgence IoT Hub :][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0204_2016-->