## Création d’une identité d’appareil
Dans cette section, vous allez créer une application console Node.js qui crée une identité d’appareil dans le registre d’identité de votre IoT Hub. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre d’identité des appareils. Reportez-vous à la section **Registre d’identité de l’appareil** du [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, elle génère un ID d’appareil et une clé uniques auxquels votre appareil peut s’identifier lorsqu’il envoie des messages appareil-à-cloud à IoT Hub.

1. Créez un dossier vide appelé **createdeviceidentity**. Dans le dossier **createdeviceidentity**, créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
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
6. Ajoutez le code suivant pour créer une définition d’appareil dans le registre d’identité d’appareil dans votre IoT Hub. Ce code crée un appareil si l’ID correspondant n’existe pas dans le registre. Dans le cas contraire, il retourne la clé de l’appareil existant :
   
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

> [!NOTE]
> Le registre d’identité IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité. Par ailleurs, un indicateur activé/désactivé vous permet de désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-identity] pour plus d’informations.
> 
> 

## Recevoir des messages appareil-à-cloud
Dans cette section, vous allez créer une application console Node.js qui lit les messages appareil-à-cloud à partir d’IoT Hub. Un IoT Hub expose un point de terminaison compatible avec [Event Hubs][lnk-event-hubs-overview] pour vous permettre de lire les messages Appareil vers cloud. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui ne convient pas dans le cas d’un déploiement à débit élevé. Le didacticiel [Traitement de messages de type « Appareil vers cloud »][lnk-processd2c-tutorial] vous indique comment traiter les messages Appareil vers cloud à grande échelle. Le didacticiel [Prise en main des hubs d’événements][lnk-eventhubs-tutorial] fournit des informations complémentaires sur la façon de traiter les messages à partir d’Event Hubs et s’applique aux points de terminaison compatibles Event Hubs exposés par l’IoT Hub.

> [!NOTE]
> Le point de terminaison compatible Event Hubs pour lire des messages de l'appareil vers le cloud utilise toujours le protocole AMQPS.
> 
> 

1. Créez un dossier vide appelé **readdevicetocloudmessages**. Dans le dossier **readdevicetocloudmessages**, créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
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
   
   > [!NOTE]
   > Ce code repose sur l’hypothèse selon laquelle vous avez créé votre IoT Hub dans le niveau F1 (gratuit). Un IoT Hub gratuit comporte deux partitions nommées « 0 » et « 1 ». Si vous avez créé votre IoT Hub en utilisant l’un des autres niveaux tarifaires, vous devez ajuster le code pour créer un objet **MessageReceiver** pour chaque partition.
   > 
   > 
6. Ajoutez la définition de filtre suivante. Cette application utilise un filtre lorsqu’elle crée un récepteur afin que ce dernier lise uniquement les messages envoyés à l’IoT Hub une fois que le récepteur a commencé à s’exécuter. Cette opération est utile dans un environnement de test, car elle vous permet de voir l’ensemble actuel de messages, mais dans un environnement de production, votre code doit vérifier qu’il traite la totalité des messages. Pour plus d’informations, voir le didacticiel [Traiter les messages appareil-à-cloud IoT Hub][lnk-processd2c-tutorial].
   
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
7. Ajoutez le code suivant pour créer l’adresse de réception et un client AMQP :
   
    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
   
    var client = new AMQPClient(Policy.EventHub);
    ```
8. Ajoutez les deux fonctions suivantes qui impriment la sortie sur la console :
   
    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
   
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```
9. Ajoutez la fonction suivante qui sert de récepteur pour une partition donnée à l’aide du filtre :
   
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
10. Ajoutez le code suivant pour vous connecter au point de terminaison compatible avec Event Hub et démarrer les récepteurs :
    
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

<!-- Links -->

[lnk-eventhubs-tutorial]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../articles/event-hubs/event-hubs-overview.md
[lnk-processd2c-tutorial]: ../articles/iot-hub/iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0413_2016-->