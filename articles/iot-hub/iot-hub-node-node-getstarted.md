<properties
	pageTitle="Prise en main d’Azure IoT Hub pour Node.js | Microsoft Azure"
	description="Didacticiel de prise en main d’Azure IoT Hub avec Node.js. Utilisez Azure IoT Hub et Node.js avec les kits de développement logiciel (SDK) de Microsoft Azure IoT pour mettre en œuvre une solution Internet des objets."
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Prise en main d’Azure IoT Hub pour Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introduction

Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT (Internet des objets) et un serveur principal de solution. L’une des plus grandes difficultés des projets IoT consiste à connecter des appareils au serveur principal de la solution de manière fiable et sécurisée. Pour relever ce défi, IoT Hub :

- Offre une messagerie évolutive Appareil vers cloud et Cloud vers appareil fiable.
- Assure la sécurité des communications grâce aux informations d’identification de sécurité par appareil et au contrôle d’accès.
- Inclut des bibliothèques d’appareils pour les langages et les plateformes les plus courants.

Ce didacticiel vous explique les procédures suivantes :

- Utilisez le portail Azure pour créer un hub IoT.
- Créer une identité de l’appareil dans votre hub IoT.
- Créer un périphérique simulé qui envoie les données de télémétrie au serveur back-end de votre service cloud.

À la fin de ce didacticiel, vous disposerez de trois applications de console Node.js :

* **CreateDeviceIdentity**, qui crée une identité d’appareil et une clé de sécurité associée pour connecter votre appareil simulé ;
* **ReadDEviceToCloudMessages.js**, qui affiche les données de télémétrie envoyées par votre périphérique simulé ;
* **SimulatedDevice.js**, qui se connecte à votre IoT hub avec l’identité d’appareil créée précédemment et envoie un message de télémétrie chaque seconde en utilisant le protocole AMQPS.

> [AZURE.NOTE] L’article [kit de développement logiciel IoT Hub][lnk-hub-sdks] fournit des informations sur les différents kits de développement logiciels que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.

Pour réaliser ce didacticiel, vous aurez besoin des éléments suivants :

+ Node.js version 0.12.x ou version ultérieure. <br/> L’article [Prepare your development environment][lnk-dev-setup] (Préparer votre environnement de développement) décrit comment installer Node.js pour ce didacticiel sur Windows ou sur Linux.

+ Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Pour terminer, cliquez sur **Paramètres** dans le panneau IoT Hub, puis sur **Messagerie** dans le panneau **Paramètres**. Sur le panneau **Messagerie**, notez le **Nom compatible Event Hub** et **Point de terminaison compatible Event Hub**. Ces valeurs sont nécessaires lorsque vous créez votre application **read-d2c-messages**.

![][6]

Maintenant, vous avez créé votre concentrateur IoT et vous disposez d’un nom d’hôte IoT Hub, de la chaîne de connexion IoT Hub, du nom compatible Event Hub et des valeurs de point de terminaison compatible Event Hub pour suivre le reste de ce didacticiel.

## Création d’une identité d’appareil

Dans cette section, vous allez créer une application console Node.js qui crée une identité d’appareil dans le registre d’identité de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’identité des appareils. Reportez-vous à la section **Registre d’identité de l’appareil** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, elle génère un ID d’appareil et une clé uniques auxquels votre appareil peut s’identifier lorsqu’il envoie des messages appareil-à-cloud à IoT Hub.

1. Créez un dossier vide appelé **createdeviceidentity**. Dans le dossier **createdeviceidentity**, créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À l’invite de commandes, dans le dossier **createdeviceidentity**, exécutez la commande suivante pour installer le package **azure-iothub** :

    ```
    npm install azure-iothub --save
    ```

3. Dans un éditeur de texte, créez un fichier **CreateDeviceIdentity.js** dans le dossier **createdeviceidentity**.

4. Ajoutez l’instruction `require` ci-dessous au début du fichier **CreateDeviceIdentity.js** :

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Ajoutez le code suivant dans le fichier **CreateDeviceIdentity.js**, en remplaçant la valeur de l’espace réservé par la chaîne de connexion du IoT Hub créé dans la section précédente :

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Ajoutez le code suivant pour créer une définition d’appareil dans le registre d’identité d’appareil dans votre IoT Hub. Ce code crée un appareil si l’ID correspondant n’existe pas dans le registre. Dans le cas contraire, il retourne la clé de l’appareil existant :

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
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

7. Enregistrez et fermez le fichier **CreateDeviceIdentity.js**.

8. Pour exécuter l’application **createdeviceidentity**, exécutez la commande ci-après à l’invite de commandes dans le dossier createdeviceidentity :

    ```
    node CreateDeviceIdentity.js 
    ```

9. Prenez note de l’**ID de l’appareil** et de la **clé de l’appareil**. Vous en aurez besoin ultérieurement lorsque vous créerez une application qui se connecte à IoT Hub en tant qu’appareil.

> [AZURE.NOTE] Le registre d’identité IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité. Par ailleurs, un indicateur activé/désactivé vous permet de désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations.

## Recevoir des messages appareil-à-cloud

Dans cette section, vous allez créer une application console Node.js qui lit les messages appareil-à-cloud à partir d’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs-overview] pour vous permettre de lire les messages Appareil vers cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traitement de messages de type « Appareil vers cloud][lnk-process-d2c-tutorial] vous indique comment traiter les messages Appareil vers cloud à grande échelle. Le didacticiel [Prise en main des hubs d’événements][lnk-eventhubs-tutorial] fournit des informations complémentaires sur la façon de traiter les messages à partir d’Event Hubs et s’applique aux points de terminaison compatibles Event Hubs exposés par l’IoT Hub.

> [AZURE.NOTE] Le point de terminaison compatible Event Hubs pour lire des messages de l'appareil vers le cloud utilise toujours le protocole AMQPS.

1. Créez un dossier vide appelé **readdevicetocloudmessages**. Dans le dossier **readdevicetocloudmessages**, créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À l’invite de commandes, dans le dossier **readdevicetocloudmessages**, exécutez la commande suivante pour installer les packages **amqp10** et **bluebird** :

    ```
    npm install amqp10 bluebird --save
    ```

3. Dans un éditeur de texte, créez un fichier **ReadDeviceToCloudMessages.js** dans le dossier **readdevicetocloudmessages**.

4. Ajoutez les instructions `require` ci-dessous au début du fichier **ReadDeviceToCloudMessages.js** :

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. Ajoutez les déclarations de variable suivantes, en remplaçant les espaces réservés par les valeurs que vous avez notées précédemment. La valeur de l’espace réservé **{your event hub-compatible namespace}** provient du champ **point de terminaison compatible avec Event Hub** dans le portail. Elle prend la forme **namespace.servicebus.windows.net** (sans le préfixe **sb://*).

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] Ce code repose sur l’hypothèse selon laquelle vous avez créé votre IoT Hub dans le niveau F1 (gratuit). Un IoT Hub gratuit comporte deux partitions nommées « 0 » et « 1 ». Si vous avez créé votre IoT Hub en utilisant l’un des autres niveaux tarifaires, vous devez ajuster le code pour créer un objet **MessageReceiver** pour chaque partition.

6. Ajoutez la définition de filtre suivante. Cette application utilise un filtre lorsqu’elle crée un récepteur afin que ce dernier lise uniquement les messages envoyés à l’IoT Hub une fois que le récepteur a commencé à s’exécuter. Cette opération est utile dans un environnement de test, car elle vous permet de voir l’ensemble actuel de messages, mais dans un environnement de production, votre code doit vérifier qu’il traite la totalité des messages. Pour plus d’informations, voir le didacticiel [Traiter les messages appareil-à-cloud IoT Hub][lnk-process-d2c-tutorial].

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. Ajoutez le code suivant pour créer l’adresse de réception et un client AMQP :

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. Ajoutez les deux fonctions suivantes qui impriment la sortie sur la console :

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. Ajoutez la fonction suivante qui sert de récepteur pour une partition donnée à l’aide du filtre :

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. Ajoutez le code suivant pour vous connecter au point de terminaison compatible avec Event Hub et démarrer les récepteurs :

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. Enregistrez et fermez le fichier **ReadDeviceToCloudMessages.js**.

## Création d’une application de périphérique simulé

Dans cette section, vous allez créer une application console Node.js qui simule un appareil envoyant des messages appareil-à-cloud à un IoT Hub.

1. Créez un dossier vide appelé **simulateddevice**. Dans le dossier **simulateddevice**, créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À l’invite de commandes, dans le dossier **simulateddevice**, exécutez la commande suivante pour installer le package **azure-iot-device-amqp** :

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

5. Ajoutez une variable **connectionString** et utilisez-la pour créer un client d’appareil. Remplacez la chaîne **{youriothubname}** par le nom de votre IoT Hub, et les chaînes **{yourdeviceid}** et **{yourdevicekey}** par les valeurs d’appareil que vous avez générées dans la section *Création d’une identité d’appareil* :

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Ajoutez la fonction suivante pour afficher la sortie de l’application :

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Créez un rappel et utilisez la fonction **setInterval** pour envoyer un nouveau message vers votre IoT Hub toutes les secondes :

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

8. Ouvrez la connexion à votre IoT Hub et commencez à envoyer des messages :

    ```
    client.open(connectCallback);
    ```

9. Enregistrez et fermez le fichier **SimulatedDevice.js**.

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].


## Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier **readdevicetocloudmessages**, exécutez la commande suivante pour commencer à analyser votre IoT Hub :

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. À l’invite de commandes du dossier **simulateddevice**, exécutez la commande suivante pour commencer à envoyer des données de télémétrie à votre IoT Hub :

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. La vignette **Utilisation** du [portail Azure][lnk-portal] indique le nombre de messages envoyés au concentrateur :

    ![][43]

## Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau concentrateur IoT dans le portail, puis créé une identité d’appareil dans le registre d’identités du concentrateur. Vous avez utilisé cette identité d’appareil pour activer l'appareil simulé qui envoie des messages de l’appareil vers le cloud au concentrateur et créé une application qui affiche les messages reçus par le concentrateur. Vous pouvez continuer à explorer les fonctionnalités d’IoT Hub et d’autres scénarios IoT dans les didacticiels suivants :

- [Envoyer des messages du cloud vers des appareils avec IoT Hub][lnk-c2d-tutorial] montre comment envoyer des messages aux appareils et traiter les informations de remise générées par IoT Hub.
- [Traiter les messages des appareils vers le cloud][lnk-process-d2c-tutorial] montre comment traiter de manière fiable des messages interactifs et de télémétrie provenant d’appareils.
- [Téléchargement de fichiers à partir d’appareils][lnk-upload-tutorial], qui décrit un modèle qui utilise les messages du cloud vers les appareils pour faciliter les téléchargements de fichiers à partir d’appareils.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->