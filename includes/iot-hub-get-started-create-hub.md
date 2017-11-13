## <a name="create-an-iot-hub"></a>Créer un hub IoT
Créez un IoT Hub pour que votre application de périphérique simulé puisse se connecter. Les étapes suivantes vous montrent comment exécuter cette tâche à l’aide du portail Azure.

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Maintenant que vous avez créé un IoT Hub, recherchez les informations importantes que vous utilisez pour connecter des appareils et des applications à votre IoT Hub. 

1. Lorsque la création du IoT Hub a réussi, cliquez sur la nouvelle vignette représentant votre concentrateur dans le portail Azure pour ouvrir la fenêtre des propriétés de votre nouveau concentrateur. Notez la valeur **Hostname**, puis cliquez sur **Stratégies d’accès partagé**.
   
    ![Nouvelle fenêtre IoT Hub][4]
1. Dans le panneau **Stratégies d’accès partagé**, cliquez sur la stratégie **iothubowner**, puis copiez et notez la chaîne de connexion IoT Hub dans la fenêtre **iothubowner**. Consultez la rubrique [Access Control][lnk-access-control] du « Guide du développeur IoT Hub » pour plus d’informations.
   
    ![Stratégies d’accès partagé][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
