<properties
   pageTitle="Connectez un périphérique à l’aide de Node.js | Microsoft Azure"
   description="Explique comment connecter un périphérique à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite dans Node.js."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/26/2016"
   ms.author="dobett"/>


# Connexion de votre appareil à la solution préconfigurée de surveillance à distance (Node.js)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Générer et exécuter l'exemple de solution node.js

1. Pour cloner le dépôt GitHub des *SDK Microsoft Azure IoT* et installer le *SDK Microsoft Azure IoT device pour Node.js* dans votre environnement de bureau, suivez les instructions contenues dans [Préparation de votre environnement de développement][lnk-github-prepare].

2. À partir de votre copie locale du dépôt [azure-iot-sdks][lnk-github-repo], copiez les deux fichiers suivants, contenus dans le dossier noeud/périphérique/exemples, dans un dossier sur votre périphérique :

  - packages.json
  - remote\_monitoring.js

3. Ouvrez le fichier remote\_monitoring.js et recherchez la variable suivante :

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Remplacez **[chaîne de connexion d’un périphérique de IoT Hub]** par votre chaîne de connexion de périphérique. Vous pouvez trouver les valeurs pour le nom d’hôte IoT Hub, l’ID de périphérique et la clé de périphérique sur le tableau de bord de solution de surveillance à distance. La chaîne de connexion du périphérique suit ce format :

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Si votre nom d’hôte IoT Hub est **contoso** et votre id de périphérique est **monpériphérique**, votre chaîne de connexion doit ressembler à ceci :

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Enregistrez le fichier. Exécutez les commandes suivantes dans une invite de commandes dans le dossier contenant ces fichiers pour installer les packages nécessaires, puis exécutez l'exemple d'application :

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-node-installers]: https://nodejs.org/download/
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

<!---HONumber=AcomDC_0518_2016-->