## Création d’une application de périphérique simulé

Dans cette section, vous allez créer une application console Node.js qui simule un appareil envoyant des messages appareil-à-cloud à un IoT Hub.

1. Créez un dossier vide appelé **simulateddevice**. Dans le dossier **simulateddevice**, créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À l’invite de commandes, dans le dossier **simulateddevice**, exécutez la commande suivante pour installer le package **azure-iot-device-amqp** :

    ```
    npm install azure-iot-device-amqp --save
    ```

3. Dans un éditeur de texte, créez un fichier **SimulatedDevice.js** dans le dossier **simulateddevice**.

4. Ajoutez les instructions `require` ci-dessous au début du fichier **SimulatedDevice.js** :

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Ajoutez une variable **connectionString** et utilisez-la pour créer un client d’appareil. Remplacez la chaîne **{youriothubname}** par le nom de votre IoT Hub, et les chaînes **{yourdeviceid}** et **{yourdevicekey}** par les valeurs d’appareil que vous avez générées dans la section *Création d’une identité d’appareil* :

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Ajoutez la fonction suivante pour afficher la sortie de l’application :

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Créez un rappel et utilisez la fonction **setInterval** pour envoyer un nouveau message vers votre IoT Hub toutes les secondes :

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. Ouvrez la connexion à votre IoT Hub et commencez à envoyer des messages :

    ```
    client.open(connectCallback);
    ```

9. Enregistrez et fermez le fichier **SimulatedDevice.js**.

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!------HONumber=AcomDC_0309_2016-->