---
title: "Mettre à jour un microprogramme avec Azure IoT Hub | Microsoft Docs"
description: "Ce didacticiel montre comment effectuer une mise à jour de microprogramme"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 7cbb823f2d8b9a337bd987ae3fc41a85ddb6ae80


---
# <a name="tutorial-how-to-do-a-firmware-update"></a>Didacticiel : Mettre à jour un microprogramme
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Introduction
Dans le didacticiel [Prise en main de la gestion d’appareils][lnk-dm-getstarted], vous avez vu comment utiliser les primitives de [représentation d’appareil physique][lnk-devtwin] et de [méthodes directives][lnk-c2dmethod] pour redémarrer à distance un appareil. Ce didacticiel utilise les mêmes primitives IoT Hub et montre comment effectuer une mise à jour du microprogramme simulée de bout en bout.  Ce modèle est utilisé dans l’implémentation de la mise à jour du microprogramme de l’exemple [d’implémentation d’appareil Raspberry Pi][lnk-rpi-implementation].

Ce didacticiel vous explique les procédures suivantes :

* Créez une application console qui appelle une méthode directe firmwareUpdate sur l’application d’appareil simulé via votre IoT Hub.
* Créez une application d’appareil simulé qui implémente une méthode directe firmwareUpdate qui passe par un processus en plusieurs étapes consistant à attendre avant de télécharger l’image du microprogramme, à télécharger celle-ci, puis finalement à l’appliquer.  Tout au long du processus, l’appareil utilise les propriétés signalées pour mettre à jour la progression.

À la fin de ce didacticiel, vous avez une application d’appareil de console Node.js et une application principale de console .NET (c#) :

**dmpatterns_fwupdate_service.js**, qui appelle une méthode directe sur l’application d’appareil simulé, affiche la réponse, et affiche périodiquement (toutes les 500 ms) les propriétés signalées mises à jour.

**TriggerFWUpdate**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, reçoit une méthode directe firmwareUpdate, s’exécute pour simuler une mise à jour du microprogramme dans un processus à plusieurs états, consistant à attendre avant de télécharger l’image, à télécharger celle-ci, puis finalement à l’appliquer.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Microsoft Visual Studio 2015.
* Node.js version 0.12.x ou version ultérieure. <br/>  L’article [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Node.js pour ce didacticiel sur Windows ou sur Linux.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

Pour créer votre IoT Hub et obtenir votre chaîne de connexion, procédez de la manière décrite dans [Prise en main de la gestion d’appareils](iot-hub-csharp-node-device-management-get-started.md).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Déclencher une mise à jour du microprogramme à distance sur l’appareil à l’aide d’une méthode directe
Dans cette section, vous créez une application console .NET (en C#) qui lance une mise à jour du microprogramme à distance sur un appareil à l’aide d’une méthode directe, et utilise des requêtes de la représentation d’appareil pour obtenir régulièrement l’état de la mise à jour du microprogramme active sur cet appareil.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **TriggerFWUpdate**.

    ![Nouveau projet Visual C# Bureau classique Windows][img-createapp]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **TriggerFWUpdate**, puis cliquez sur **Gérer les packages NuGet**.
3. Dans la fenêtre **Gestionnaire de package Nuget**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement, l’installation et ajoute une référence au package Nuget [kit de développement logiciel (SDK) de service Microsoft Azure IoT][lnk-nuget-service-sdk] et ses dépendances.

    ![Fenêtre du gestionnaire de package Nuget][img-servicenuget]
4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur des espaces réservés par la chaîne de connexion pour le IoT Hub créé dans la section précédente.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. Ajoutez la méthode suivante à la classe **Program** :
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Ajoutez la méthode suivante à la classe **Program** :

        public static async Task StartFirmwareUpdate()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);
            method.SetPayloadJson(
                @"{
                    fwPackageUri : 'https://someurl'
                }");

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Générez la solution.

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous allez :

* Créer une application console Node.js qui répond à une méthode directe appelée par le cloud
* Déclencher une mise à jour du microprogramme simulé
* Utiliser les propriétés signalées pour activer les requêtes sur la représentation d’appareil afin d’identifier les appareils et l’heure de leur dernière mise à jour de microprogramme

1. Créez un dossier vide nommé **simulateddevice**.  Dans le dossier **simulateddevice**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes.  Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour installer les packages Kit de développement logiciel (SDK) pour appareils **azure-iot-device** et **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_fwupdate_device.js** dans le dossier **manageddevice**.
4. Ajoutez les instructions ’require’ suivantes au début du fichier **dmpatterns_fwupdate_device.js** :
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Ajoutez une variable **connectionString** et utilisez-la pour créer un client d’appareil.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Ajoutez la fonction suivante qui sera utilisée pour mettre à jour les propriétés signalées
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
7. Ajoutez les fonctions suivantes qui simulent le téléchargement et l’application de l’image du microprogramme.
   
    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
   
      console.log("Downloading image from " + imageUrl);
   
      callback(error, image);
    }
   
    var simulateApplyImage = function(imageData, callback) {
      var error = null;
   
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
   
      callback(error);
    }
    ```
8. Ajoutez la fonction suivante qui actualise l’état de mise à jour du microprogramme (en le définissant sur En attente de téléchargement) via les propriétés signalées.  Généralement, un appareil est informé de la disponibilité d’une mise à jour, et une stratégie définie par un administrateur a pour effet que l’appareil commence à télécharger et à appliquer la mise à jour.  C’est ici qu’intervient la logique d’activation de cette stratégie.  Pour plus de facilité, nous retardons de 4 secondes et procédons au téléchargement de l’image du microprogramme. 
   
    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
   
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
9. Ajoutez la fonction suivante qui actualise l’état de mise à jour du microprogramme (en le définissant sur En attente de téléchargement) via les propriétés signalées.  Elle poursuit en simulant le téléchargement du microprogramme, puis actualise l’état de mise à jour du microprogramme afin d’informer sur la réussite ou l’échec du téléchargement.
   
    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
   
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
   
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
   
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
   
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
   
      }, 4000);
    }
    ```
10. Ajoutez la fonction suivante qui actualise l’état de mise à jour du microprogramme (en le définissant sur En attente d’application de l’image du microprogramme) via les propriétés signalées.  Elle poursuit en simulant l’application de l’image du microprogramme, puis actualise l’état de mise à jour du microprogramme afin d’informer sur la réussite ou l’échec de l’application.
    
    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
    
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
    
      setTimeout(function() {
    
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
    
          }
        });
    
        setTimeout(callback, 4000);
    
      }, 4000);
    }
    ```
11. Ajoutez la fonction suivante qui gère la méthode **firmwareUpdate** et lance le processus en plusieurs étapes de mise à jour du microprogramme.
    
    ```
    var onFirmwareUpdate = function(request, response) {
    
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });
    
      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;
    
      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
    
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });
    
        }
      });
    }
    ```
12. Enfin, ajoutez le code suivant qui connecte à votre IoT Hub en tant qu’appareil. 
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].
> 
> 

## <a name="run-the-apps"></a>Exécuter les applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Exécutez l’application console C# **TriggerFWUpdate**, cliquez avec le bouton droit sur le projet **TriggerFWUpdate**, puis sélectionnez **Debug** et **Démarrer une nouvelle instance**.

3. La réponse de l’appareil à la méthode directe s’affiche dans la console.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher une mise à jour du microprogramme à distance sur un appareil, et utilisé périodiquement les propriétés signalées pour comprendre la progression du processus de mise à jour du microprogramme.  

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/


<!--HONumber=Nov16_HO5-->


