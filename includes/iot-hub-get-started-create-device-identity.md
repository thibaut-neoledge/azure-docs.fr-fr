## <a name="create-a-device-identity"></a>Création d’une identité d’appareil

Dans cette section, vous utilisez un outil Node.js appelé [iothub-explorer][iot-hub-explorer] pour créer une identité d’appareil pour ce didacticiel.

1. Dans votre environnement de ligne de commande, exécutez la commande suivante :

    `npm install -g iothub-explorer@latest`

1. Exécutez ensuite la commande ci-dessous pour vous connecter à votre hub. Remplacez `{iot hub connection string}` par la chaîne de connexion IoT Hub que vous avez copié précédemment :

    `iothub-explorer login "{iot hub connection string}"`

1. Enfin, créez une nouvelle identité d’appareil appelée `myDeviceId` avec la commande :

    `iothub-explorer create myDeviceId --connection-string`

Prenez note de la chaîne de connexion de l’appareil à partir du résultat. Cette chaîne de connexion est utilisée par l’application d’appareil pour se connecter à votre IoT Hub en tant qu’appareil.

![][img-identity]

Reportez-vous à [Prise en main d’IoT Hub][lnk-getstarted] pour créer des identités d’appareil par programme.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
