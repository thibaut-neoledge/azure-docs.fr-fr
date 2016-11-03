<properties
    pageTitle="Envoi de messages cloud-à-appareil avec IoT Hub | Microsoft Azure"
    description="Suivez ce didacticiel pour découvrir comment envoyer des messages cloud-à-appareil à l’aide d’Azure IoT Hub avec Java."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>


# <a name="tutorial-how-to-send-cloudtodevice-messages-with-iot-hub-and-nodejs"></a>Didacticiel : envoi de messages cloud-à-appareil avec IoT Hub et Node.js

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Le didacticiel [Prise en main d’IoT Hub] explique comment créer un concentrateur IoT, l’utiliser pour configurer une identité d’appareil et coder une simulation d’appareil qui envoie des messages d’appareils à cloud.

Ce didacticiel s’appuie sur l’article [Prise en main d’Azure IoT Hub]. Cette rubrique vous explique les procédures suivantes :

- À partir du back end de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.
- Recevez des messages cloud-à-appareil sur un appareil.
- À partir du back end de votre application, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages cloud-à-appareil dans le [Guide du développeur d’IoT Hub][[Guide du développeur d’IoT Hub - CD2].

À la fin de ce didacticiel, vous exécuterez deux applications de console Node.js :

* **SimulatedDevice**, une version modifiée de l’application créée dans [Prise en main d’IoT Hub]qui se connecte à votre hub IoT et reçoit les messages entre cloud et appareils.
* **SendCloudToDeviceMessage**, qui envoie un message cloud-à-appareil à l’appareil simulé par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [AZURE.NOTE] IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel, et à Azure IoT Hub de manière générale, consultez le [Centre de développement Azure IoT].

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

+ Node.js version 0.10.x ou ultérieure.

+ Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Version d’évaluation gratuite d’Azure][lnk-free-trial].)

## <a name="receive-messages-on-the-simulated-device"></a>Réception de messages sur le périphérique simulé

Dans cette section, vous modifiez l’application de l’appareil simulé que vous avez créée dans [Prise en main d’Azure IoT Hub] pour recevoir des messages cloud-à-appareil à partir d’IoT Hub.

1. À l’aide d’un éditeur de texte, ouvrez le fichier SimulatedDevice.js.

2. Modifiez la fonction **connectCallback** pour gérer les messages envoyés à partir d’IoT Hub. Dans cet exemple, l’appareil appelle toujours la fonction **complete** afin de notifier IoT Hub qu’il a traité le message. La nouvelle version de la fonction **connectCallback** ressemble à ceci :

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] Si vous utilisez HTTP/1 au lieu d’AMQP ou de MQTT pour le transport, l’instance **DeviceClient** vérifie les messages à partir d’IoT Hub peu fréquemment (moins de toutes les 25 minutes). Pour plus d’informations sur les différences entre la prise en charge d’AMQP, de MQTT et de HTTP/1, ainsi que la limitation d’IoT Hub, voir le [Guide du développeur d’IoT Hub][Guide du développeur d’IoT Hub - CD2].

## <a name="send-a-cloudtodevice-message"></a>Envoi d’un message cloud vers appareil

Dans cette section, vous allez créer une application de console Node.js qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé. Vous avez besoin de l’ID de l’appareil que vous avez ajouté dans le didacticiel [Prise en main d’IoT Hub] . Vous avez également besoin de la chaîne de connexion pour votre IoT Hub que vous trouverez dans le [portail Azure].

1. Créez un dossier vide appelé **sendcloudtodevicemessage**. Dans le dossier **sendcloudtodevicemessage** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À l’invite de commandes, dans le dossier **sendcloudtodevicemessage**, exécutez la commande suivante pour installer le package **azure-iothub** :

    ```
    npm install azure-iothub --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **SendCloudToDeviceMessage.js** dans le dossier **sendcloudtodevicemessage**.

4. Ajoutez les instructions `require` ci-dessous au début du fichier **SendCloudToDeviceMessage.js** :

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Ajoutez le code suivant au fichier **SendCloudToDeviceMessage.js** . Remplacez la valeur d’espace réservé par la chaîne de connexion pour le IoT Hub créé dans le didacticiel [Prise en main d’IoT Hub] . Remplacez l’espace réservé pour l’appareil cible par l’ID de l’appareil ajouté dans le didacticiel [Prise en main d’IoT Hub] :

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Ajoutez la fonction suivante pour imprimer les résultats de l’opération sur la console :

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Ajoutez la fonction suivante pour imprimer les messages d’accusé de réception sur la console :

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Ajoutez le code suivant pour envoyer un message à votre appareil et gérer le message de commentaires lorsque l’appareil accuse réception d’un message cloud-à-appareil :

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. Enregistrez et fermez le fichier **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier **simulateddevice** , exécutez la commande suivante pour envoyer la télémétrie à IoT hub et écouter les messages cloud-à-appareil :

    ```
    node SimulatedDevice.js 
    ```

    ![Exécution de l’application de périphérique simulé][img-simulated-device]

2. À l’invite de commandes dans le dossier **sendcloudtodevicemessage** , exécutez la commande suivante pour envoyer un message cloud-à-appareil, puis attendez de recevoir l’accusé de réception :

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![Exécutez l’application pour envoyer la commande c2d][img-send-command]

    > [AZURE.NOTE] Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Transient Fault Handling](Gestion des erreurs temporaires).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages cloud-à-appareil. 

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Azure IoT Suite].

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Prise en main d’IoT Hub]: iot-hub-node-node-getstarted.md
[Guide du développeur d’IoT Hub - CD2]: iot-hub-devguide-messaging.md
[Guide du développeur d’IoT Hub]: iot-hub-devguide.md
[Centre de développement Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portail Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/


<!---HONumber=Oct16_HO2-->


