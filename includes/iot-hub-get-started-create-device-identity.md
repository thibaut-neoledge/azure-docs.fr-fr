## <a name="create-a-device-identity"></a>Création d’une identité d’appareil
Dans cette section, vous utilisez un outil Node.js appelé [IoT Hub Explorer][iot-hub-explorer] pour créer une identité d’appareil pour ce didacticiel.

1. Dans votre environnement de ligne de commande, exécutez la commande suivante :
   
    npm install -g iothub-explorer@latest
2. Ensuite, exécutez la commande suivante pour vous connecter à votre hub. N’oubliez pas de remplacer `{iot hub connection string}` par la chaîne de connexion IoT Hub que vous avez copiée précédemment :
   
    iothub-explorer login "{chaîne de connexion à iot hub}"
3. Enfin, créez une nouvelle identité d’appareil appelée `myDeviceId` avec la commande :
   
    iothub-explorer create myDeviceId --connection-string

Prenez note de la chaîne de connexion de l’appareil à partir du résultat. Cette chaîne de connexion est utilisée par l’application d’appareil pour se connecter à votre IoT Hub en tant qu’appareil.

![][img-identity]

Reportez-vous à [Prise en main d’IoT Hub][lnk-getstarted] pour savoir comment créer des identités d’appareil par programme.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md


<!--HONumber=Dec16_HO1-->


