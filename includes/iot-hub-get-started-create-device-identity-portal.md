## <a name="create-a-device-identity"></a>Création d’une identité d’appareil
Dans cette section, vous allez utiliser le [Portail Azure][lnk-azure-portal] afin de créer une identité d’appareil dans le registre d’identité présent dans votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre des identités. Reportez-vous à la section Registre d’identité du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Le **Device Explorer** présent sur le portail vous aide à générer un ID d’appareil et une clé uniques, pouvant être utilisés par votre appareil afin de s’identifier lorsqu’il envoie des messages de l’appareil au cloud vers IoT Hub.

1. Assurez-vous d’être connecté au [Portail Azure][lnk-azure-portal]. 

1. Dans la barre de lancement, cliquez sur **All resources** (Toutes les ressources) et recherchez votre ressource IoT Hub. 
   
    ![Accéder à votre IoT Hub][img-find-iothub]
1. Lorsque votre ressource IoT Hub est ouverte, cliquez sur l’outil **Device Explorer**, puis cliquez en haut sur **Ajouter**. Indiquez le nom de votre nouvel appareil et cliquez sur **Enregistrer**. 

    ![Créer une identité d’appareil sur le portail][img-create-device]

   Cela permet de créer une identité d’appareil pour votre IoT Hub.
1. Dans la liste d’appareil du **Device Explorer**, cliquez sur le périphérique récemment créé et notez la **chaîne de connexion ainsi que la clé primaire**. 

    ![Chaîne de connexion de l’appareil][img-connection-string]
> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity].
> 
> 

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

