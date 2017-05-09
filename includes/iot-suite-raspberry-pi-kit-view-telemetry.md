## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

Le Raspberry Pi prend désormais en charge l’envoi de données de télémétrie à la solution de surveillance à distance. Vous pouvez afficher les données de télémétrie sur le tableau de bord de la solution. Vous pouvez également envoyer des messages à votre Raspberry Pi à partir du tableau de bord de la solution.

- Accédez au tableau de bord de la solution.
- Sélectionnez votre appareil dans la liste déroulante **Appareil à afficher**.
- Les données de télémétrie du Raspberry Pi s’affichent sur le tableau de bord.

![Afficher les données de télémétrie depuis le Raspberry Pi][img-telemetry-display]

## <a name="act-on-the-device"></a>Action sur l’appareil

À partir du tableau de bord de la solution, vous pouvez appeler des méthodes sur votre Raspberry Pi. Lorsque le Raspberry Pi se connecte à la solution de surveillance à distance, il envoie des informations sur les méthodes qu’il prend en charge.

- Dans le tableau de bord de la solution, cliquez sur **Appareils** pour accéder à la page **Appareils**. Sélectionnez votre Raspberry Pi dans la **Liste des appareils**. Choisissez ensuite **Méthodes** :

    ![Répertorier les appareils dans le tableau de bord][img-list-devices]

- Dans la page **Appeler une méthode**, choisissez **LightBlink** dans la liste déroulante **Méthode**.

- Choisissez **InvokeMethod**. La LED connectée au Raspberry Pi clignote plusieurs fois. L’application sur le Raspberry Pi renvoie un accusé de réception sur le tableau de bord de la solution :

    ![Afficher l’historique des méthodes][img-method-history]

- Vous pouvez allumer et éteindre le voyant en utilisant la méthode **ChangeLightStatus** et en définissant le paramètre **LightStatusValue** sur la valeur **1** pour l’allumer, ou sur la valeur **0** pour l’éteindre.

> [!WARNING]
> Si vous laissez la solution de surveillance à distance s’exécuter dans votre compte Azure, vous serez facturé pour son temps d’exécution. Pour plus d’informations sur la manière de réduire votre consommation pendant l’exécution de la solution de surveillance à distance, consultez [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuration des solutions préconfigurées Azure IoT Suite à des fins de démonstration). Supprimez la solution préconfigurée de votre compte Azure lorsque vous avez fini de l’utiliser.


[img-telemetry-display]: media/iot-suite-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md