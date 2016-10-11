## Créer un hub IoT

Créez un hub IoT pour que votre périphérique simulé puisse se connecter. Les étapes suivantes vous montrent comment exécuter cette tâche à l’aide du portail Azure.

1. Connectez-vous au [portail Azure][lnk-portal].

2. Dans la barre de lancement, cliquez sur **Nouveau** > **Internet des objets** > **Azure IoT Hub**.

    ![Barre de lancement du portail Azure][1]

3. Dans le panneau **IoT hub**, choisissez la configuration de votre concentrateur IoT.

    ![Panneau IoT Hub][2]

    * Dans la zone **Nom**, saisissez un nom pour identifier votre hub IoT. Une fois le **Nom** validé et disponible, une coche verte apparaît dans la case **Nom**.
    * Sélectionnez une [tarification et un niveau de mise à l’échelle][lnk-pricing]. Ce didacticiel ne nécessite pas un niveau spécifique. Pour ce didacticiel, utilisez le niveau F1 gratuit.
    * Dans **Groupe de ressources**, créez un groupe de ressources Azure ou sélectionnez un groupe existant. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure][lnk-resource-groups].
    * Dans **Emplacement**, sélectionnez l’emplacement destiné à héberger votre concentrateur IoT. Pour ce didacticiel, choisissez l’emplacement le plus proche.

4. Une fois que vous avez choisi les options de configuration de votre hub IoT, cliquez sur **Créer**. La création du hub IoT par Azure peut prendre plusieurs minutes. Pour vérifier l’état d’avancement de l’opération, vous pouvez consulter le tableau d’accueil ou le panneau Notifications.

    ![Nouveau statut IoT Hub][3]

5. Lorsque l’IoT hub a été créé avec succès, cliquez sur la nouvelle vignette représentant votre IoT hub dans le portail pour ouvrir le panneau du nouveau IoT hub. Notez la valeur **Hostname**, puis cliquez sur **Stratégies d’accès partagé**.

    ![Nouveau panneau IoT Hub][4]

6. Dans le panneau **Stratégies d’accès partagé**, cliquez sur la stratégie **iothubowner**, puis copiez et notez la chaîne de connexion dans le panneau **iothubowner**. Consultez la rubrique [Access Control][lnk-access-control] du "guide du développeur Azure IoT Hub" pour plus d’informations.

    ![Panneau des stratégies d’accès partagées][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md

<!---HONumber=AcomDC_1005_2016-->