## <a name="view-device-telemetry-in-the-dashboard"></a>Affichage de la télémétrie des appareils dans le tableau de bord
Le tableau de bord de la solution de surveillance à distance permet d'afficher la télémétrie que vos appareils envoient au IoT Hub.

1. Dans votre navigateur, revenez au tableau de bord de la solution de surveillance à distance, cliquez sur **Périphériques** dans le panneau de gauche pour accéder à la **Liste de périphériques**.
2. Dans la **Liste d’appareils**, vous devez voir que l’état de votre appareil est maintenant **En cours d’exécution**. Sinon, cliquez sur **Activer l’appareil** dans le panneau **Détails de l’appareil**.
   
    ![Afficher l’état de l’appareil][18]
3. Cliquez sur **Tableau de bord** pour revenir au tableau de bord, sélectionnez votre périphérique dans la liste déroulante **Périphérique à afficher** pour afficher sa télémétrie. La télémétrie de l’exemple d’application est configurée pour envoyer 50 unités correspondant à la température interne, 55 unités correspondant à la température externe et 50 à l’humidité.
   
    ![Afficher la télémétrie d’appareil][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>Appel d’une méthode sur votre appareil
Le tableau de bord de la solution de surveillance à distance vous permet d’appeler des méthodes sur vos appareils via IoT Hub. Par exemple, dans la solution de surveillance à distance, vous pouvez appeler une méthode pour simuler le redémarrage d’un appareil.

1. Dans le tableau de bord de la solution de surveillance à distance, cliquez sur **Périphériques** dans le panneau de gauche pour accéder à la **Liste de périphériques**.
2. Cliquez sur **ID de périphérique** pour votre périphérique dans la **Liste de périphériques**.
3. Dans le panneau **Détails de l’appareil**, cliquez sur **Méthodes**.
   
    ![Méthodes d’appareil][13]
4. Dans la liste déroulante **Méthode**, sélectionnez **InitiateFirmwareUpdate**, puis dans **FWPACKAGEURI**, saisissez une URL factice. Cliquez sur **Appeler une méthode** pour appeler la méthode sur l’appareil.
   
    ![Appel d’une méthode d’appareil][14]
   

5. Vous voyez un message dans la console exécutant votre code d’appareil lorsque l’appareil traite la méthode. Les résultats de la méthode sont ajoutés à l’historique dans le portail de solution :

    ![Affichage de l’historique des méthodes][img-method-history]

## <a name="next-steps"></a>Étapes suivantes
L'article [Personnalisation des solutions préconfigurées][lnk-customize] décrit quelques méthodes pour étendre cet exemple. Les extensions incluent l'utilisation de capteurs réels et l'implémentation de commandes supplémentaires.

Vous pouvez en savoir plus sur les [autorisations sur le site azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-v1-visualize-connecting/suite4.png
[14]: ./media/iot-suite-v1-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-v1-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-v1-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-v1-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
