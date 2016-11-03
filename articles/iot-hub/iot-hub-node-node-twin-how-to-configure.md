<properties
    pageTitle="Utilisez des propriétés de représentation | Microsoft Azure"
    description="Ce didacticiel montre comment utiliser des propriétés de représentation"
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>


# <a name="tutorial-use-desired-properties-to-configure-devices-preview"></a>Didacticiel : Utiliser des propriétés souhaitées pour configurer des appareils (version préliminaire)

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

À la fin de ce didacticiel, vous disposerez de deux applications console Node.js :

* **SimulateDeviceConfiguration.js**, application d’appareil simulée qui attend une mise à jour de configuration souhaitée, et signale l’état d’un processus de mise à jour de configuration simulée.
* **SetDesiredConfigurationAndQuery.js**, application Node.js destinée à être exécuté à partir du serveur principal, qui définit la configuration souhaitée sur un appareil, et interroge le processus de mise à jour de configuration.

> [AZURE.NOTE] L’article [Kits de développement logiciel (SDK) IoT Hub][lnk-hub-sdks] fournit des informations sur les différents kits de développement logiciels que vous pouvez utiliser pour générer les applications d’appareil et de serveur principal.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

+ Node.js version 0.10.x ou ultérieure.

+ Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Version d’évaluation gratuite d’Azure][lnk-free-trial].)

Si vous avez suivi le didacticiel [Prise en main des représentations d’appareils][lnk-twin-tutorial], vous avez déjà un concentrateur activé pour la gestion d’appareil et une identité d’appareil nommée **myDeviceId**. Vous pouvez passer à la section [Créer l’application d’appareil simulée][lnk-how-to-configure-createapp].

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Créer l’application d’appareil simulée

Dans cette section, vous créez une application console Node.js qui se connecte à votre concentrateur en tant que **myDeviceId**, attend une mise à jour de la configuration souhaitée, puis signale des mises à jour sur le processus de mise à jour de configuration simulée.

1. Créez un dossier vide nommé **simulatedeviceconfiguration**. Dans le dossier **simulatedeviceconfiguration**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À l’invite de commandes, dans le dossier **simulatedeviceconfiguration**, exécutez la commande suivante pour installer les packages **azure-iot-device** et **azure-iot-device-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **SimulateDeviceConfiguration.js** dans le dossier **simulatedeviceconfiguration**.

4. Ajoutez le code suivant au fichier **SimulateDeviceConfiguration.js**, puis remplacez l’espace réservé **{device connection string}** par la chaîne de connexion que vous avez copiée lors de la création de l’identité d’appareil **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });

    L’objet **Client** expose toutes les méthodes requises pour interagir avec des représentations d’appareil à partir de l’appareil. Le code précédent, après avoir initialisé l’objet **Client**, récupère la représentation de **myDeviceId**, puis attache un gestionnaire pour la mise à jour sur les propriétés souhaitées. Le gestionnaire vérifie l’existence d’une demande de modification de configuration réelle en comparant les configIds, puis appelle une méthode qui démarre la modification de configuration.

    Notez que, par souci de simplicité, le code précédent utilise une valeur par défaut codées en dur pour la configuration initiale. Une application réelle chargerait probablement la configuration à partir d’un stockage local.
    
    > [AZURE.IMPORTANT] Les événements de modification de propriété souhaitée étant toujours émis une seule fois lors de la connexion de l’appareil, assurez-vous de l’existence d’une modification réelle dans les propriétés souhaitées avant d’exécuter toute action.

5. Avant d’appeler `client.open()`ajoutez les méthodes suivantes :

        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";

            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }

        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
            
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;

            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };

    La méthode **initConfigChange** met à jour les propriétés signalées sur l’objet de représentation local avec la demande de mise à jour de configuration, et définit l’état sur **Pending** (En attente), puis met à jour la représentation d’appareil sur le service. Après la mise à jour de la représentation, elle simule un processus de longue durée qui s’achève par l’exécution de **completeConfigChange**. Cette méthode met à jour les propriétés signalées de la représentation locale en définissant l’état sur **Success** (Réussite) et en supprimant l’objet **pendingConfig**. Ensuite, elle met à jour la représentation sur le service.

    Notez que, pour économiser la bande passante, les propriétés signalées sont mises à jour en spécifiant uniquement les propriétés à modifier (nommées **patch** dans le code ci-dessus), au lieu de remplacer le document entier.

    > [AZURE.NOTE] Ce didacticiel ne simule pas de comportement pour des mises à jour de configuration simultanées. Certains processus de mise à jour de configuration peuvent s’adapter à des modifications de configuration de la cible en cours de mise à jour, d’autres peuvent avoir à les mettre en file d’attente, et d’autres encore peuvent les refuser avec une condition d’erreur. Prenez en considération le comportement souhaité pour votre processus de configuration spécifique, et ajoutez la logique appropriée avant de lancer la modification de la configuration.

6. Exécutez l’application d’appareil :

        node SimulateDeviceConfiguration.js

    Vous devriez voir le message `retrieved device twin`. Gardez l’application active.

## <a name="create-the-service-app"></a>Créer l’application de service

Dans cette section, vous allez créer une application console Node.js qui met à jour les *propriétés souhaitées* sur la représentation associée à **myDeviceId** avec un nouvel objet de configuration de télémétrie. Elle interroge ensuite le représentations d’appareil stockées dans le concentrateur, puis affiche la différence entre les configurations souhaitées et signalées de l’appareil.

1. Créez un dossier vide nommé **setdesiredandqueryapp**. Dans le dossier **setdesiredandqueryapp**, créez un fichier package.json à l’aide de la commande ci-dessous à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À l’invite de commandes, dans le dossier **setdesiredandqueryapp**, exécutez la commande suivante pour installer le package **azure-iothub** :

    ```
    npm install azure-iothub@dtpreview node-uuid --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **SetDesiredAndQuery.js** dans le dossier **addtagsandqueryapp**.

4. Ajoutez le code suivant au fichier **SetDesiredAndQuery.js**, puis remplacez l’espace réservé **{service connection string}** par la chaîne de connexion que vous avez copiée lors de la création de votre concentrateur :

        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{service connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
         
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });
            

    L’objet **Registry** expose toutes les méthodes requises pour interagir avec des représentations d’appareil à partir du service. Le code précédent, après avoir initialisé l’objet **Registry**, récupère la représentation de **myDeviceId**, puis met à jour ses propriétés souhaitées avec un nouvel objet de configuration de télémétrie. Ensuite, il appelle l’événement de fonction **queryTwins** pendant 10 secondes.

    > [AZURE.IMPORTANT] Cette application interroge IoT Hub toutes les 10 secondes à titre d’exemple. Utilisez des requêtes pour générer des rapports côté utilisateur sur plusieurs appareils, non pour détecter des modifications. Si votre solution nécessite des notifications d’événements d’appareil en temps réel, utilisez des [messages appareil-à-cloud][lnk-d2c].

5. Ajoutez le code suivant juste devant l’appel de `registry.getDeviceTwin()` pour implémenter la fonction **queryTwins**:

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };

    Le code précédent interroge les représentations stockées dans le concentrateur, et imprime les configurations de télémétrie souhaitées et signalées. Pour savoir comment générer des rapports riches sur tous vos appareils, voir le [Langage de requête IoT Hub][lnk-query].


8. Avec **SimulateDeviceConfiguration.js** en cours d’exécution, exécutez l’application avec :

        node SetDesiredAndQuery.js 5m

    Vous devriez voir la configuration signalée passer de **Success** (Réussite) à **Pending** (En attente), puis de nouveau à **Success** (Réussite), avec la nouvelle fréquence d’envoi active de cinq minutes au lieu de 24 heures.

    > [AZURE.IMPORTANT] Un délai pouvant atteindre une minute s’écoule entre l’opération de signalement de l’appareil et le résultat de la requête. Il permet à l’infrastructure de requête d’opérer à très grande échelle. Pour récupérer des vues cohérentes d’une représentation, utilisez la méthode **getDeviceTwin** dans la classe **Registry**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez défini une configuration souhaitée en tant que *propriétés souhaitées* à partir d’une application principale, et écrit une application d’appareil simulée pour détecter cette modification et simuler un processus de mise à jour en plusieurs étapes signalant son état en tant que *propriétés signalées* à la représentation.

Utilisez les ressources suivantes :

- Pour savoir comment envoyer la télémétrie d’appareils, voir le didacticiel [Prise en main d’IoT Hub][lnk-iothub-getstarted].
- Pour savoir comment planifier ou exécuter des opérations sur de grands ensembles d’appareils, voir le didacticiel [Utiliser des travaux pour planifier et diffuser des opérations d’appareil][lnk-schedule-jobs].
- Pour savoir comment contrôler des appareils de façon interactive (par exemple, mettre en marche un ventilateur à partir d’une application contrôlée par l’utilisateur), voir le didacticiel [Utiliser des méthodes directes][lnk-methods-tutorial].


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app



<!--HONumber=Oct16_HO2-->


