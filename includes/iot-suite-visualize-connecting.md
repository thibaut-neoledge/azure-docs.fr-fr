## Affichage de la télémétrie des appareils dans le tableau de bord

Le tableau de bord de la solution de surveillance à distance permet d'afficher la télémétrie que vos appareils envoient au IoT Hub.

1. Dans votre navigateur, retournez au tableau de bord de la solution de surveillance à distance, cliquez sur **Périphériques** dans le panneau de gauche pour accéder à la **Liste de périphériques**.

2. Dans la **Liste de périphériques**, vous devez voir que l'état de votre périphérique est maintenant **En cours d'exécution**.

    ![][18]

3. Dans le tableau de bord, sélectionnez votre périphérique dans la liste déroulante **Périphérique à afficher** pour afficher sa télémétrie. La télémétrie de l’exemple d’application est configurée pour envoyer 50 unités correspondant à la température interne, 55 unités correspondant à la température externe et 50 à l’humidité. Notez que le tableau de bord affiche uniquement les données de température et d’humidité par défaut.

    ![][img-telemetry]

## Envoyer une commande à votre périphérique

Le tableau de bord de la solution de surveillance à distance permet de demander à IoT Hub d’envoyer des commandes à vos périphériques. Par exemple, dans la solution de surveillance à distance, vous pouvez envoyer une commande pour définir la température interne d'un périphérique.

1. Dans le tableau de bord de la solution de surveillance à distance, cliquez sur **Périphériques** dans le panneau de gauche pour accéder à la **Liste de périphériques**.

2. Cliquez sur **ID de périphérique** pour votre périphérique dans la **Liste de périphériques**.

3. Dans le panneau **Détails du périphérique**, cliquez sur **Commandes**.

    ![][13]

4. Dans la liste déroulante **Commande**, sélectionnez **SetTemperature**, puis dans **Température**, saisissez une nouvelle valeur de température. Cliquez sur **Envoyer commande** pour envoyer la commande au périphérique.

    ![][14]

    > [AZURE.NOTE]L'historique affiche initialement l'état de la commande comme étant **En attente**. Lorsque le périphérique reconnaît la commande, l'état passe à **Réussite**.

5. Dans le tableau de bord, vérifiez que le périphérique envoie désormais 75 comme nouvelle valeur de température.

## Étapes suivantes

L'article [Personnalisation des solutions préconfigurées][lnk-customize] décrit quelques méthodes pour étendre cet exemple. Les extensions incluent l'utilisation de capteurs réels et l'implémentation de commandes supplémentaires.

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-dev-messaging]: ../articles/iot-hub/iot-hub-devguide.md#messaging

<!---HONumber=AcomDC_1125_2015-->