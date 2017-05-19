---
title: "Connexion d’un appareil à l’aide de Node.js | Microsoft Docs"
description: "Explique comment connecter un périphérique à la solution de surveillance à distance Azure IoT Suite préconfigurée à l’aide d’une application écrite dans Node.js."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 8f45d0e86a95779d5ceeddb72638b14e0e7a80eb
ms.contentlocale: fr-fr
ms.lasthandoff: 04/10/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Connexion de votre appareil à la solution préconfigurée de surveillance à distance (Node.js)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-nodejs-sample-solution"></a>Création d’un exemple de solution Node.js

Vérifiez que Node.js version 0.11.5 ou ultérieure est installé sur votre ordinateur de développement. Vous pouvez exécuter `node --version` dans la ligne de commande pour vérifier la version.

1. Créez un dossier nommé **RemoteMonitoring** sur votre ordinateur de développement. Accédez à ce dossier dans votre environnement de ligne de commande.

1. Exécutez les commandes suivantes pour télécharger et installer les packages dont vous avez besoin pour terminer l’exemple d’application :

    ```
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Dans le dossier **RemoteMonitoring**, créez un fichier appelé **remote_monitoring.js**. Ouvrez ce fichier dans un éditeur de texte.

1. Dans le fichier **remote_monitoring.js**, ajoutez les instructions `require` suivantes :

    ```nodejs
    'use strict';

    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

1. Ajoutez les déclarations de variables suivantes après les instructions `require` . Remplacez les valeurs d’espace réservé [Id d’appareil] et [Clé d’appareil] par les valeurs que vous avez notées pour votre appareil provenant du tableau de bord de la solution de surveillance à distance. Utilisez le nom d’hôte IoT Hub du tableau de bord de la solution pour remplacer [Nom IoTHub]. Par exemple, si votre nom d’hôte IoT Hub est **contoso.azure-devices.net**, remplacez [Nom Hub IoT] par **contoso** :

    ```nodejs
    var connectionString = 'HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]';
    var deviceId = ConnectionString.parse(connectionString).DeviceId;
    ```

1. Ajoutez les variables suivantes pour définir des données de télémétrie de base :

    ```nodejs
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    ```

1. Ajoutez la fonction d’assistance suivante pour imprimer les résultats de l’opération :

    ```nodejs
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Ajoutez la fonction d’assistance suivante qui permet de rendre aléatoires les valeurs de télémétrie :

    ```nodejs
    function generateRandomIncrement() {
        return ((Math.random() * 2) - 1);
    }
    ```

1. Ajoutez la définition suivante pour l’objet **DeviceInfo** envoyé par l’appareil au démarrage :

    ```nodejs
    var deviceMetaData = {
        'ObjectType': 'DeviceInfo',
        'IsSimulatedDevice': 0,
        'Version': '1.0',
        'DeviceProperties': {
            'DeviceID': deviceId,
            'HubEnabledState': 1
        }
    };
    ```

1. Ajoutez la définition suivante pour les valeurs signalées du jumeau d’appareil. Cette définition inclut les descriptions des méthodes directes prises en charge par l’appareil :

    ```nodejs
    var reportedProperties = {
        "Device": {
            "DeviceState": "normal",
            "Location": {
                "Latitude": 47.642877,
                "Longitude": -122.125497
            }
        },
        "Config": {
            "TemperatureMeanValue": 56.7,
            "TelemetryInterval": 45
        },
        "System": {
            "Manufacturer": "Contoso Inc.",
            "FirmwareVersion": "2.22",
            "InstalledRAM": "8 MB",
            "ModelNumber": "DB-14",
            "Platform": "Plat 9.75",
            "Processor": "i3-9",
            "SerialNumber": "SER99"
        },
        "Location": {
            "Latitude": 47.642877,
            "Longitude": -122.125497
        },
        "SupportedMethods": {
            "Reboot": "Reboot the device",
            "InitiateFirmwareUpdate--FwPackageURI-string": "Updates device Firmware. Use parameter FwPackageURI to specifiy the URI of the firmware file"
        },
    }
    ```

1. Ajoutez la fonction suivante pour gérer l’appel de méthode directe **Reboot** :

    ```nodejs
    function onReboot(request, response) {
        // Implement actual logic here.
        console.log('Simulated reboot...');

        // Complete the response
        response.send(200, "Rebooting device", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

1. Ajoutez la fonction suivante pour gérer l’appel de méthode directe **InitiateFirmwareUpdate**. Cette méthode directe utilise un paramètre pour spécifier l’emplacement de l’image du microprogramme à télécharger, puis lance la mise à jour du microprogramme de façon asynchrone sur l’appareil :

    ```nodejs
    function onInitiateFirmwareUpdate(request, response) {
        console.log('Simulated firmware update initiated, using: ' + request.payload.FwPackageURI);

        // Complete the response
        response.send(200, "Firmware update initiated", function(err) {
            if(!!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });

        // Add logic here to perform the firmware update asynchronously
    }
    ```

1. Ajoutez le code suivant pour créer une instance de client :

    ```nodejs
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

1. Ajoutez le code suivant à :

    * Ouvrir la connexion.
    * Envoyer un objet **DeviceInfo**.
    * Définir un gestionnaire pour les propriétés souhaitées.
    * Envoyer les propriétés signalées.
    * Inscrire des gestionnaires pour les méthodes directes.
    * Démarrer l’envoi de la télémétrie.

    ```nodejs
    client.open(function (err) {
        if (err) {
            printErrorFor('open')(err);
        } else {
            console.log('Sending device metadata:\n' + JSON.stringify(deviceMetaData));
            client.sendEvent(new Message(JSON.stringify(deviceMetaData)), printErrorFor('send metadata'));

            // Create device twin
            client.getTwin(function(err, twin) {
                if (err) {
                    console.error('Could not get device twin');
                } else {
                    console.log('Device twin created');

                    twin.on('properties.desired', function(delta) {
                        console.log('Received new desired properties:');
                        console.log(JSON.stringify(delta));
                    });

                    // Send reported properties
                    twin.properties.reported.update(reportedProperties, function(err) {
                        if (err) throw err;
                        console.log('twin state reported');
                    });

                    // Register handlers for direct methods
                    client.onDeviceMethod('Reboot', onReboot);
                    client.onDeviceMethod('InitiateFirmwareUpdate', onInitiateFirmwareUpdate);
                }
            });

            // Start sending telemetry
            var sendInterval = setInterval(function () {
                temperature += generateRandomIncrement();
                externalTemperature += generateRandomIncrement();
                humidity += generateRandomIncrement();

                var data = JSON.stringify({
                    'DeviceID': deviceId,
                    'Temperature': temperature,
                    'Humidity': humidity,
                    'ExternalTemperature': externalTemperature
                });

                console.log('Sending device event data:\n' + data);
                client.sendEvent(new Message(data), printErrorFor('send event'));
            }, 5000);

            client.on('error', function (err) {
                printErrorFor('client')(err);
                if (sendInterval) clearInterval(sendInterval);
                client.close(printErrorFor('client.close'));
            });
        }
    });
    ```

1. Enregistrez les modifications dans le fichier **remote_monitoring.js**.

1. Exécutez la commande suivante à l’invite de commande pour démarrer l’exemple d’application :
   
    ```
    node remote_monitoring.js
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdk-node
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

