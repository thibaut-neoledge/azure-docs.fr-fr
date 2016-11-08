---
title: Prise en main d’Azure IoT Hub pour Node.js | Microsoft Docs
description: Didacticiel de prise en main d’Azure IoT Hub avec Node.js. Utilisez Azure IoT Hub et Node.js avec les Kits de développement logiciel (SDK) Microsoft Azure IoT pour mettre en œuvre une solution Internet des objets.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2016
ms.author: dobett

---
# <a name="get-started-with-azure-iot-hub-for-node.js"></a>Prise en main d’Azure IoT Hub pour Node.js
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous disposez de trois applications console Node.js :

* **CreateDeviceIdentity**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre appareil simulé ;
* **ReadDEviceToCloudMessages.js**, qui affiche les données de télémétrie envoyées par votre appareil simulé ;
* **SimulatedDevice.js**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment et envoie un message de télémétrie chaque seconde en utilisant le protocole AMQPS.

> [!NOTE]
> L’article [kit de développement logiciel IoT Hub][lnk-hub-sdks] fournit des informations sur les différents kits de développement logiciels que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Node.js version 0.10.x ou version ultérieure.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite][lnk-free-trial] d’Azure.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Votre IoT Hub est maintenant créé. Vous disposez du nom d’hôte et de la chaîne de connexion de l’IoT Hub dont vous avez besoin pour effectuer la suite du didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité d’appareil
Dans cette section, vous créez une application console Node.js qui crée une identité d’appareil dans le registre d’identité de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’identité des appareils. Reportez-vous à la section **Registre d’identité de l’appareil** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, elle génère un ID d’appareil et une clé uniques que votre appareil peut utiliser pour s’identifier lorsqu’il envoie des messages appareil-à-cloud à IoT Hub.

1. Créez un dossier vide appelé **createdeviceidentity**. Dans le dossier **createdeviceidentity** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **createdeviceidentity**, exécutez la commande suivante pour installer le package SDK du service **azure-iothub** :
   
    ```
    npm install azure-iothub --save
    ```
3. Dans un éditeur de texte, créez un fichier **CreateDeviceIdentity.js** dans le dossier **createdeviceidentity**.
4. Ajoutez l’instruction `require` ci-dessous au début du fichier **CreateDeviceIdentity.js** :
   
    ```
    'use strict';
   
    var iothub = require('azure-iothub');
    ```
5. Ajoutez le code suivant dans le fichier **CreateDeviceIdentity.js** , en remplaçant la valeur de l’espace réservé par la chaîne de connexion du IoT Hub créé dans la section précédente : 
   
    ```
    var connectionString = '{iothub connection string}';
   
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```
6. Ajoutez le code suivant pour créer une définition d’appareil dans le registre d’identité d’appareil dans votre IoT Hub. Ce code crée un appareil si l’ID correspondant n’existe pas dans le registre. Dans le cas contraire, il retourne la clé de l’appareil existant :
   
    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });
   
    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```
7. Enregistrez et fermez le fichier **CreateDeviceIdentity.js** .
8. Pour exécuter l’application **createdeviceidentity** , exécutez la commande ci-après à l’invite de commandes dans le dossier createdeviceidentity :
   
    ```
    node CreateDeviceIdentity.js 
    ```
9. Prenez note de l’**ID de l’appareil** et de la **clé de l’appareil**. Vous aurez besoin de ces valeurs ultérieurement lorsque vous créerez une application qui se connecte à IoT Hub en tant qu’appareil.

> [!NOTE]
> Le registre d’identité IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID et les clés des appareils à utiliser comme informations d’identification de sécurité, et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations.
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages appareil-à-cloud
Dans cette section, vous créez une application console Node.js qui lit les messages appareil-à-cloud à partir d’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs-overview] pour vous permettre de lire les messages Appareil vers cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traiter les messages des appareils vers le cloud IoT Hub][lnk-process-d2c-tutorial] vous indique comment traiter les messages des appareils vers le cloud à grande échelle. Le didacticiel [Prise en main des hubs d’événements][lnk-eventhubs-tutorial] fournit des informations complémentaires sur le traitement des messages des hubs d’événements et s’applique aux points de terminaison compatibles avec les hubs d’événements de l’IoT Hub.

> [!NOTE]
> Le point de terminaison compatible Event Hubs pour lire des messages de l'appareil vers le cloud utilise toujours le protocole AMQPS.
> 
> 

1. Créez un dossier vide appelé **readdevicetocloudmessages**. Dans le dossier **readdevicetocloudmessages** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **readdevicetocloudmessages**, exécutez la commande suivante pour installer le package **azure-event-hubs** :
   
    ```
    npm install azure-event-hubs --save
    ```
3. Dans un éditeur de texte, créez un fichier **ReadDeviceToCloudMessages.js** dans le dossier **readdevicetocloudmessages**.
4. Ajoutez les instructions `require` ci-dessous au début du fichier **ReadDeviceToCloudMessages.js** :
   
    ```
    'use strict';
   
    var EventHubClient = require('azure-event-hubs').Client;
    ```
5. Ajoutez la déclaration de variable suivante et remplacez la valeur de l’espace réservé par la chaîne de connexion pour votre IoT Hub :
   
    ```
    var connectionString = '{iothub connection string}';
    ```
6. Ajoutez les deux fonctions suivantes qui impriment la sortie sur la console :
   
    ```
    var printError = function (err) {
      console.log(err.message);
    };
   
    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```
7. Ajoutez le code ci-dessous pour créer l’instance **EventHubClient**, ouvrez la connexion à votre IoT Hub et créez un récepteur pour chaque partition. Cette application utilise un filtre lorsqu’elle crée un récepteur afin que ce dernier lise uniquement les messages envoyés à l’IoT Hub une fois que le récepteur a commencé à s’exécuter. Dans un environnement de test, ce filtre permet de voir seulement l’ensemble des messages en cours. Dans un environnement de production, votre code doit vérifier qu’il traite la totalité des messages. Pour plus d’informations, consultez le didacticiel [Traiter les messages des appareils vers le cloud IoT Hub][lnk-process-d2c-tutorial] :
   
    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```
8. Enregistrez et fermez le fichier **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous créez une application console Node.js qui simule un appareil envoyant des messages appareil-à-cloud à un IoT Hub.

1. Créez un dossier vide appelé **simulateddevice**. Dans le dossier **simulateddevice** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **simulateddevice**, exécutez la commande suivante pour installer le package device SDK **azure-iot-device** et le package **azure-iot-device-amqp** :
   
    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```
3. Dans un éditeur de texte, créez un fichier **SimulatedDevice.js** dans le dossier **simulateddevice**.
4. Ajoutez les instructions `require` ci-dessous au début du fichier **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```
5. Ajoutez une variable **connectionString** et utilisez-la pour créer un client d’appareil. Remplacez **{youriothostname}** par le nom de l’IoT hub que vous avez créé dans la section *Créer un IoT Hub* . Remplacez **{yourdevicekey}** par la valeur clé d’appareil que vous avez générée dans la section *Création d’une identité d’appareil* :
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
   
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
7. Créez un rappel et utilisez la fonction **setInterval** pour envoyer un nouveau message à votre IoT Hub toutes les secondes :
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
   
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
8. Ouvrez la connexion à votre IoT Hub et commencez à envoyer des messages :
   
    ```
    client.open(connectCallback);
    ```
9. Enregistrez et fermez le fichier **SimulatedDevice.js** .

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].
> 
> 

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **readdevicetocloudmessages** , exécutez la commande suivante pour commencer à surveiller votre IoT Hub :
   
    ```
    node ReadDeviceToCloudMessages.js 
    ```
   
    ![Application cliente Node.js du service IoT Hub pour surveiller les messages appareil-à-cloud][7]
2. À l’invite de commandes, dans le dossier **simulateddevice** , exécutez la commande suivante pour commencer à envoyer des données de télémétrie à votre IoT Hub :
   
    ```
    node SimulatedDevice.js
    ```
   
    ![Application cliente Node.js des appareils IoT Hub pour surveiller les messages appareil-à-cloud][8]
3. La vignette **Utilisation** du [portail Azure][lnk-portal] indique le nombre de messages envoyés au hub :
   
    ![Vignette Utilisation du portail Azure indiquant le nombre de messages envoyés à IoT Hub][43]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail, puis créé une identité d’appareil dans le registre d’identités du concentrateur. Vous avez utilisé cette identité d’appareil pour permettre à l’appareil simulé d’envoyer des messages Appareil vers cloud au concentrateur. Vous avez également créé une application qui affiche les messages reçus par le concentrateur. 

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Connexion de votre appareil][lnk-connect-device]
* [Prise en main de la gestion d’appareils][lnk-device-management]
* [Prise en main du Kit de développement logiciel (SDK) Gateway][lnk-gateway-SDK]

Pour découvrir comment étendre votre solution IoT et traiter les messages des appareils vers le cloud à grande échelle, consultez le didacticiel [Traiter les messages des appareils vers le cloud][lnk-process-d2c-tutorial].

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Oct16_HO2-->


