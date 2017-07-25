## <a name="create-an-iot-hub"></a>Créer un hub IoT

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Nouveau** > **Internet des objets** > **IoT Hub**.

   ![Créer un IoT Hub dans le portail Azure](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
2. Dans le volet **IoT Hub**, entrez les informations suivantes pour votre IoT Hub :

     **Nom** : entrez le nom de votre IoT Hub. Si le nom saisi est valide, une coche verte s’affiche.

     **Niveau de tarification et de mise à l’échelle** : sélectionnez le niveau **F1 gratuit**. Cette option suffit pour cette démonstration. Pour plus d’informations, consultez [Niveau de tarification et de mise à l’échelle](https://azure.microsoft.com/pricing/details/iot-hub/).

     **Groupe de ressources** : créez un groupe de ressources pour héberger l’IoT Hub ou utilisez-en un existant. Pour plus d’informations, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../articles/azure-resource-manager/resource-group-portal.md).

     **Emplacement** : sélectionnez l’emplacement le plus proche de l’endroit où vous créez l’IoT Hub.

     **Épingler au tableau de bord** : Sélectionnez cette option pour pouvoir accéder facilement à votre instance IoT Hub à partir du tableau de bord.

   ![Entrer des informations pour créer votre IoT hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

3. Cliquez sur **Create**. La création de votre IoT Hub peut prendre plusieurs minutes. Vous pouvez suivre la progression dans le volet **Notifications**.

   ![Voir les notifications de progression de votre IoT Hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

4. Une fois votre IoT Hub créé, cliquez dessus dans le tableau de bord. Notez la valeur **Hostname**, puis cliquez sur **Stratégies d’accès partagé**.

   ![Obtention du nom d’hôte de votre IoT Hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

5. Dans le volet **Stratégies d’accès partagé**, cliquez sur la stratégie **iothubowner**, puis copiez et notez la **chaîne de connexion** de votre IoT Hub. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

   ![Obtention de la chaîne de connexion de votre IoT Hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Inscrire un appareil dans l’IoT Hub pour votre appareil

1. Dans le [portail Azure](https://portal.azure.com/), ouvrez votre instance IoT Hub.

2. Cliquez sur **Explorateur d’appareils**.
3. Dans le volet Explorateur d’appareils, cliquez sur **Ajouter** pour ajouter un appareil à votre IoT Hub. Faites ensuite ce qui suit :

   **ID d’appareil** : entrez l’ID du nouvel appareil. Les ID d’appareil respectent la casse.

   **Type d’authentification** : sélectionnez **Clé symétrique**.

   **Générer automatiquement les clés** : cochez cette case.

   **Connecter l’appareil à IoT Hub** : cliquez sur **Activer**.

   ![Ajouter un appareil dans l’outil Device Explorer de votre IoT Hub](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Cliquez sur **Save**.
5. Une fois que l’appareil est créé, ouvrez-le dans le volet **Explorateur d’appareils**.
6. Notez la clé primaire de la chaîne de connexion.

   ![Obtention de la chaîne de connexion de l’appareil](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)