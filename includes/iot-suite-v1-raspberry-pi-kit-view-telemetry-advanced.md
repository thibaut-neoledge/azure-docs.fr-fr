## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

Le Raspberry Pi prend désormais en charge l’envoi de données de télémétrie à la solution de surveillance à distance. Vous pouvez afficher les données de télémétrie sur le tableau de bord de la solution. Vous pouvez également envoyer des messages à votre Raspberry Pi à partir du tableau de bord de la solution.

- Accédez au tableau de bord de la solution.
- Sélectionnez votre appareil dans la liste déroulante **Appareil à afficher**.
- Les données de télémétrie du Raspberry Pi s’affichent sur le tableau de bord.

![Afficher les données de télémétrie depuis le Raspberry Pi][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>Initier la mise à jour du microprogramme

Le processus de mise à jour du microprogramme télécharge et installe une version mise à jour de l’application cliente pour appareil sur le Raspberry Pi. Pour plus d’informations sur le processus de mise à jour du microprogramme, consultez la description du modèle de mise à jour du microprogramme dans [Vue d’ensemble de la gestion des appareils avec IoT Hub][lnk-update-pattern].

Initiez le processus de mise à jour du microprogramme en appelant une méthode sur l’appareil. Cette méthode est asynchrone et revient dès que le processus de mise à jour commence. L’appareil utilise des propriétés signalées pour informer la solution de la progression de la mise à jour.

Vous appelez des méthodes sur votre Raspberry Pi à partir du tableau de bord de la solution. Lorsque le Raspberry Pi se connecte en premier à la solution de surveillance à distance, il envoie des informations sur les méthodes qu’il prend en charge. 

[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
