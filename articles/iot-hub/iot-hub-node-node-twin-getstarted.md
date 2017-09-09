---
title: "Bien démarrer avec les jumeaux d’appareil Azure IoT Hub (Node) | Microsoft Docs"
description: "Guide d’utilisation des jumeaux d’appareil Azure IoT Hub pour ajouter des balises, puis utiliser une requête IoT Hub. Vous utilisez les SDK Azure IoT pour Node.js afin d’implémenter l’application d’appareil simulé et une application de service qui ajoute les balises et exécute la requête IoT Hub."
services: iot-hub
documentationcenter: node
author: fsautomata
manager: timlt
editor: 
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 633c9fd4f8a1d017d93148f8c2e860ccba14238c
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

---
# <a name="get-started-with-device-twins-node"></a>Bien démarrer avec les jumeaux d’appareil (Node)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

À la fin de ce didacticiel, vous disposerez de deux applications console Node.js :

* **AddTagsAndQuery.js**, application Node.js qui ajoute des balises et interroge des jumeaux d’appareil.
* **TwinSimulatedDevice.js**, application Node.js qui simule un appareil se connectant à votre IoT Hub avec l’identité d’appareil créée précédemment, et signale son état de connectivité.

> [!NOTE]
> L’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks] fournit des informations sur les kits SDK IoT que vous pouvez utiliser pour générer des applications d’appareil et des applications backend.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Node.js version 0.10.x ou ultérieure.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Créer l’application de service
Dans cette section, vous créez une application console Node.js qui ajoute des métadonnées d’emplacement au jumeau d’appareil associé à **myDeviceId**. Elle interroge ensuite les jumeaux d’appareil stockés dans le hub IoT en sélectionnant les appareils situés aux États-Unis, puis ceux qui signalent une connexion mobile.

1. Créez un dossier vide nommé **addtagsandqueryapp**. Dans le dossier **addtagsandqueryapp**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **addtagsandqueryapp**, exécutez la commande suivante pour installer le package **azure-iothub** :
   
    ```
    npm install azure-iothub --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **AddTagsAndQuery.js** dans le dossier **addtagsandqueryapp**.
4. Ajoutez le code suivant au fichier **AddTagsAndQuery.js**, puis remplacez l’espace réservé **{iot hub connection string}** par la chaîne de connexion que vous avez copiée lors de la création de votre IoT Hub :
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    L’objet **Registry** expose toutes les méthodes requises pour interagir avec des jumeaux d’appareil à partir du service. Le code précédent initialise l’objet **Registry**, récupère le jumeau d’appareil de **myDeviceId**, puis met à jour ses balises avec les informations d’emplacement souhaitées.
   
    Une fois les balises mises à jour, il appelle la fonction **queryTwins**.
5. Ajoutez le code suivant à la fin de  **AddTagsAndQuery.js** pour implémenter la fonction **queryTwins**:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    Le code précédent exécute deux requêtes : la première sélectionne uniquement les jumeaux d’appareil situés dans l’usine **Redmond43**et la seconde affine la requête pour sélectionner uniquement les appareils qui sont également connectés via un réseau mobile.
   
    Notez que le code précédent, quand il crée l’objet **query**, spécifie un nombre maximal de documents retournés. L’objet **query** contient une propriété booléenne **hasMoreResults** permettant d’appeler les méthodes **nextAsTwin** plusieurs fois afin de récupérer tous les résultats. Une méthode appelée **next** est disponible pour les résultats qui ne sont pas des jumeaux d’appareil, par exemple, les résultats de requêtes d’agrégation.
6. Exécutez l’application avec :
   
        node AddTagsAndQuery.js
   
    Vous devriez voir un appareil dans les résultats de la requête demandant tous les appareils situés à **Redmond43**, et aucun pour la requête limitant les résultats aux appareils utilisant un réseau mobile.
   
    ![][1]

Dans la section suivante, vous allez créer une application d’appareil qui signale les informations de connectivité et modifie le résultat de la requête de la section précédente.

## <a name="create-the-device-app"></a>Créer l’application d’appareil
Dans cette section, vous allez créer une application console Node.js qui se connecte à votre hub en tant que **myDeviceId**, puis met à jour les propriétés signalées de son jumeau d’appareil afin qu’elles contiennent les informations indiquant qu’elle est connectée par le biais d’un réseau mobile.

> [!NOTE]
> Actuellement, les jumeaux d’appareil sont accessibles uniquement à partir d’appareils qui se connectent à IoT Hub à l’aide du protocole MQTT. Pour obtenir des instructions sur la conversion d’une application d’appareil existante pour utiliser MQTT, voir [Support MQTT][lnk-devguide-mqtt].
> 
> 

1. Créez un dossier vide nommé **reportconnectivity**. Dans le dossier **reportconnectivity**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **reportconnectivity**, exécutez la commande suivante pour installer les packages **azure-iot-device** et **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **ReportConnectivity.js** dans le dossier **reportconnectivity**.
4. Ajoutez le code suivant au fichier **ReportConnectivity.js**, puis remplacez l’espace réservé **{device connection string}** par la chaîne de connexion à l’appareil que vous avez copiée lors de la création de l’identité d’appareil **myDeviceId** :
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    L’objet **Client** expose toutes les méthodes requises pour interagir avec des jumeaux d’appareil à partir de l’appareil. Le code précédent, après avoir initialisé l’objet **Client**, récupère le jumeau d’appareil de **myDeviceId**, puis met à jour sa propriété signalée avec les informations de connectivité.
5. Exécuter l’application d’appareil
   
        node ReportConnectivity.js
   
    Vous devriez voir le message `twin state reported`.
6. À présent que l’appareil a signalé ses informations de connectivité, il doit apparaître dans les deux requêtes. Accédez au dossier **addtagsandqueryapp**, puis réexécutez les requêtes :
   
        node AddTagsAndQuery.js
   
    Cette fois, **myDeviceId** doit apparaître dans les résultats des deux requêtes.
   
    ![][3]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré un nouveau hub IoT dans le portail Azure, puis créé une identité d’appareil dans le registre des identités du hub IoT. Vous avez ajouté des métadonnées d’appareil en tant que balises à partir d’une application backend et écrit une application d’appareil simulée pour signaler des informations de connectivité d’appareil dans le jumeau d’appareil. Vous avez également appris à interroger ces informations à l’aide du langage de requête IoT Hub de type SQL.

Utilisez les ressources suivantes :

* Pour savoir comment envoyer les données de télémétrie à partir d’appareils, consultez le didacticiel [Prise en main d’IoT Hub][lnk-iothub-getstarted].
* Pour savoir comment configurer des appareils à l’aide des propriétés de jumeau d’appareil souhaitées, consultez le didacticiel [Utiliser des propriétés souhaitées pour configurer des appareils][lnk-twin-how-to-configure].
* Pour savoir comment contrôler des appareils de façon interactive (par exemple en mettant en marche un ventilateur à partir d’une application contrôlée par l’utilisateur), consultez le didacticiel [Utiliser des méthodes directes][lnk-methods-tutorial].

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

