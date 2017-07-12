---
title: "Utilisation des propriétés des représentations physiques Azure IoT Hub (.NET/Node) | Microsoft Docs"
description: "Guide d’utilisation des représentations d’appareils Azure IoT Hub pour configurer des appareils. Vous utilisez Azure IoT device SDK pour Node.js afin d’implémenter une application d’appareil simulé et Azure IoT service SDK pour .NET afin d’implémenter une application de service qui modifie une configuration d’appareil avec une représentation d’appareil."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: ca5ee2733df51faa5025c4d8eb687c81df4a3b4f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/09/2017

---
<a id="use-desired-properties-to-configure-devices" class="xliff"></a>

# Utilisation des propriétés souhaitées pour configurer des appareils
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

À la fin de ce didacticiel, vous disposerez de deux applications console :

* **SimulateDeviceConfiguration.js**, application d’appareil simulée qui attend une mise à jour de configuration souhaitée, et signale l’état d’un processus de mise à jour de configuration simulée.
* **SetDesiredConfigurationAndQuery**, application .NET sur le serveur principal qui définit la configuration souhaitée sur un appareil, et interroge le processus de mise à jour de configuration.

> [!NOTE]
> L’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks] fournit des informations sur les Kits de développement logiciel (SDK) IoT que vous pouvez utiliser pour générer des applications pour appareil et des applications principales.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Node.js version 0.10.x ou ultérieure.
* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.

Si vous avez suivi le didacticiel [Prise en main des jumeaux d’appareils][lnk-twin-tutorial], vous avez déjà un IoT Hub et une identité d’appareil nommée **myDeviceId**. Vous pouvez donc passer à la section [Créer l’application d’appareil simulé][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
<a id="create-the-simulated-device-app" class="xliff"></a>

## Créer l’application d’appareil simulée
Dans cette section, vous créez une application console Node.js qui se connecte à votre hub en tant que **myDeviceId**, attend une mise à jour de la configuration souhaitée, puis signale des mises à jour sur le processus de mise à jour de configuration simulée.

1. Créez un dossier vide nommé **simulatedeviceconfiguration**. Dans le dossier **simulatedeviceconfiguration**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes. Acceptez toutes les valeurs par défaut.
   
    ```
    npm init
    ```
1. À l’invite de commandes, dans le dossier **simulatedeviceconfiguration**, exécutez la commande suivante pour installer les packages **azure-iot-device** et **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. À l’aide d’un éditeur de texte, créez un fichier **SimulateDeviceConfiguration.js** dans le dossier **simulatedeviceconfiguration**.
1. Ajoutez le code suivant au fichier **SimulateDeviceConfiguration.js**, puis remplacez l’espace réservé **{device connection string}** par la chaîne de connexion à l’appareil que vous avez copiée lors de la création de l’identité d’appareil **myDeviceId** :
   
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
   
    L’objet **Client** expose toutes les méthodes requises pour interagir avec des représentations d’appareil à partir de l’appareil. Ce code initialise l’objet **Client**, récupère le jumeau d’appareil de **myDeviceId**, puis attache un gestionnaire pour la mise à jour sur les *propriétés souhaitées*. Le gestionnaire vérifie l’existence d’une demande de modification de configuration réelle en comparant les configIds, puis appelle une méthode qui démarre la modification de configuration.
   
    Notez que, par souci de simplicité, ce code utilise une valeur par défaut codée en dur pour la configuration initiale. Une application réelle chargerait probablement la configuration à partir d’un stockage local.
   
   > [!IMPORTANT]
   > Les événements de modification de propriété souhaités sont toujours émis une seule fois lors de la connexion de l’appareil. Pensez à vérifier que les propriétés souhaitées ont bien été modifiées avant d’exécuter une action.
   > 
   > 
1. Avant d’appeler `client.open()`ajoutez les méthodes suivantes :
   
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
   
    La méthode **initConfigChange** met à jour les propriétés signalées sur l’objet de représentation d’appareils local avec la demande de mise à jour de configuration, et définit l’état sur **Pending** (En attente), puis met à jour la représentation d’appareils sur le service. Après la mise à jour de la représentation d’appareil, elle simule un processus de longue durée qui s’achève par l’exécution de **completeConfigChange**. Cette méthode met à jour les propriétés signalées locales en définissant l’état sur **Success** (Réussite) et en supprimant l’objet **pendingConfig**. Ensuite, elle met à jour la représentation d’appareils sur le service.
   
    Notez que, pour économiser la bande passante, les propriétés signalées sont mises à jour en spécifiant uniquement les propriétés à modifier (nommées **patch** dans le code ci-dessus), au lieu de remplacer le document entier.
   
   > [!NOTE]
   > Ce didacticiel ne simule pas de comportement pour des mises à jour de configuration simultanées. Certains processus de mise à jour de configuration peuvent s’adapter à des modifications de configuration de la cible en cours de mise à jour, d’autres peuvent avoir à les mettre en file d’attente, et d’autres encore peuvent les refuser avec une condition d’erreur. Prenez en considération le comportement souhaité pour votre processus de configuration spécifique, et ajoutez la logique appropriée avant de lancer la modification de la configuration.
   > 
   > 
1. Exécutez l’application d’appareil :
   
        node SimulateDeviceConfiguration.js
   
    Vous devriez voir le message `retrieved device twin`. Gardez l’application active.

<a id="create-the-service-app" class="xliff"></a>

## Créer l’application de service
Dans cette section, vous créez une application console .NET qui met à jour les *propriétés souhaitées* sur la représentation d’appareils associée à **myDeviceId** avec un nouvel objet de configuration de télémétrie. Elle interroge ensuite les représentations d’appareils stockées dans l’IoT Hub, puis affiche la différence entre les configurations souhaitées et signalées de l’appareil.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **SetDesiredConfigurationAndQuery**.
   
    ![Nouveau projet Visual C# Bureau classique Windows][img-createapp]
1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SetDesiredConfigurationAndQuery**, puis cliquez sur **Gérer les packages NuGet...**.
1. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Service SDK NuGet][lnk-nuget-service-sdk] et ses dépendances.
   
    ![Fenêtre du gestionnaire de package NuGet][img-servicenuget]
1. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion IoT Hub pour le hub créé dans la section précédente.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Ajoutez la méthode suivante à la classe **Program** :
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            try
            {
                while (true)
                {
                    var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                    var results = await query.GetNextAsTwinAsync();
                    foreach (var result in results)
                    {
                        Console.WriteLine("Config report for: {0}", result.DeviceId);
                        Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine();
                    }
                    Thread.Sleep(10000);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }
   
    L’objet **Registry** expose toutes les méthodes requises pour interagir avec des représentations d’appareil à partir du service. Ce code initialise l’objet **Registry**, récupère le jumeau d’appareils de **myDeviceId**, puis met à jour ses propriétés souhaitées avec un nouvel objet de configuration de télémétrie.
    Après cela, il interroge toutes les 10 secondes les jumeaux d’appareils stockées dans l’IoT Hub, puis imprime les configurations de télémétrie souhaitées et signalées. Pour savoir comment générer des rapports riches sur tous vos appareils, voir le [langage de requête IoT Hub][lnk-query].
   
   > [!IMPORTANT]
   > Cette application interroge IoT Hub toutes les 10 secondes à titre d’exemple. Utilisez des requêtes pour générer des rapports côté utilisateur sur plusieurs appareils, non pour détecter des modifications. Si votre solution nécessite des notifications d’événements d’appareil en temps réel, utilisez des [notifications jumelles][lnk-twin-notifications].
   > 
   > 
1. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. Dans l’Explorateur de solutions, sélectionnez **Définir les projets de démarrage...** et vérifiez que l’**Action** définie pour le projet **SetDesiredConfigurationAndQuery** est **Démarrer**. Générez la solution.
1. Avec **SimulateDeviceConfiguration.js** en cours d’exécution, exécutez l’application .NET depuis Visual Studio avec **F5** et vous devriez voir la configuration signalée passer de **Success** (Réussite) à **Pending** (En attente), puis de nouveau à **Success** (Réussite), avec la nouvelle fréquence d’envoi active de cinq minutes au lieu de 24 heures.

 ![Appareil configuré correctement][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Un délai pouvant atteindre une minute s’écoule entre l’opération de signalement de l’appareil et le résultat de la requête. Il permet à l’infrastructure de requête d’opérer à très grande échelle. Pour récupérer des vues cohérentes d’une représentation d’appareil, utilisez la méthode **getDeviceTwin** dans la classe **Registry**.
   > 
   > 

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
Dans ce didacticiel, vous avez défini une configuration souhaitée en tant que *propriétés souhaitées* à partir du serveur principal et écrit une application d’appareil pour détecter cette modification et simuler un processus de mise à jour en plusieurs étapes signalant son état par le biais des propriétés signalées.

Utilisez les ressources suivantes :

* Pour savoir comment envoyer les données de télémétrie à partir d’appareils, consultez le didacticiel [Prise en main d’IoT Hub][lnk-iothub-getstarted].
* Pour savoir comment planifier ou exécuter des opérations sur de grands ensembles d’appareils, consultez le didacticiel [Planifier et diffuser des travaux][lnk-schedule-jobs].
* Pour savoir comment contrôler des appareils de façon interactive (par exemple en mettant en marche un ventilateur à partir d’une application contrôlée par l’utilisateur), consultez le didacticiel [Utiliser des méthodes directes][lnk-methods-tutorial].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app

