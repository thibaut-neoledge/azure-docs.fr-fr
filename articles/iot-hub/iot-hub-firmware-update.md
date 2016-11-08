---
title: Mettre à jour un microprogramme | Microsoft Docs
description: Ce didacticiel montre comment effectuer une mise à jour de microprogramme
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere

---
# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>Didacticiel : Mettre à jour un microprogramme (version préliminaire)
## <a name="introduction"></a>Introduction
Dans le didacticiel [Prise en main de la gestion d’appareils][lnk-dm-getstarted], vous avez vu comment utiliser la [représentation d’appareil][lnk-devtwin] et les primitives des [méthodes cloud-à-appareil (C2D)][lnk-c2dmethod] pour redémarrer un appareil à distance. Ce didacticiel utilise les mêmes primitives IoT Hub, fournit des conseils, et montre comment effectuer une mise à jour du microprogramme simulée de bout en bout.  Ce modèle est utilisé dans l’implémentation de la mise à jour du microprogramme de l’exemple d’appareil Intel Edison.

Ce didacticiel vous explique les procédures suivantes :

* Créer une application console qui appelle la méthode directe firmwareUpdate sur l’appareil simulé via votre IoT Hub.
* Créer un appareil simulé qui implémente une méthode directe firmwareUpdate qui passe par un processus en plusieurs étapes consistant à attendre avant de télécharger l’image du microprogramme, à télécharger celle-ci, puis finalement à l’appliquer.  Tout au long du processus, l’appareil utilise les propriétés signalées de la représentation pour mettre à jour la progression.

À la fin de ce didacticiel, vous disposerez de deux applications console Node.js :

**dmpatterns_fwupdate_service.js**, qui appelle une méthode directe sur l’appareil simulé, affiche la réponse, et affiche périodiquement (toutes les 500 ms) les propriétés signalées de la représentation d’appareil mise à jour ;

**dmpatterns_fwupdate_device.js**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, reçoit une méthode directe firmwareUpdate, s’exécute pour simuler une mise à jour du microprogramme dans un processus à plusieurs états, consistant à attendre avant de télécharger l’image, à télécharger celle-ci, puis finalement à l’appliquer.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

Node.js version 0.12.x ou version ultérieure. <br/>  La rubrique [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Node.js pour ce didacticiel sur Windows ou Linux.

Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Version d’évaluation gratuite d’Azure][lnk-free-trial].)

Pour créer votre IoT Hub et obtenir votre chaîne de connexion, procédez de la manière décrite dans [Prise en main de la gestion d’appareils](iot-hub-device-management-get-started.md).

[!INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous créez une application console Node.js qui répond à une méthode directe appelée par le cloud, qui déclenche une mise à jour du microprogramme de l’appareil simulé et utilise les propriétés signalées de la représentation d’appareil pour permettre aux requêtes de la représentation d’appareil d’identifier des appareils et de déterminer le moment de leur dernier redémarrage.

1. Créez un dossier vide nommé **simulateddevice**.  Dans le dossier **simulateddevice**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes.  Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **simulateddevice**, exécutez la commande suivante pour installer le **azure-iot-device@dtpreview**package du Kit de développement logiciel (SDK) pour appareils et le **azure-iot-device-mqtt@dtpreview**package :
   
    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_fwupdate_device.js** dans le dossier **manageddevice**.
4. Ajoutez les instructions ’require’ suivantes au début du fichier **dmpatterns_fwupdate_device.js** :
   
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
6. Ajoutez la fonction suivante qui sera utilisée pour mettre à jour les propriétés signalées de la représentation.
   
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
7. Ajoutez les fonctions suivantes qui simuleront le téléchargement et l’application de l’image du microprogramme.
   
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
8. Ajoutez la fonction suivante qui actualisera l’état de mise à jour du microprogramme (en le définissant sur En attente de téléchargement) via les propriétés signalées de la représentation.  Généralement, un appareil est informé de la disponibilité d’une mise à jour, et une stratégie définie par un administrateur a pour effet que l’appareil commence à télécharger et à appliquer la mise à jour.  C’est ici qu’intervient la logique d’activation de cette stratégie.  Pour plus de facilité, nous retardons de 4 secondes et procédons au téléchargement de l’image du microprogramme. 
   
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
9. Ajoutez la fonction suivante qui actualise l’état de mise à jour du microprogramme (en le définissant sur Téléchargement de l’image du microprogramme) via les propriétés signalées de la représentation.  Elle poursuit en simulant le téléchargement du microprogramme, puis actualise l’état de mise à jour du microprogramme afin d’informer sur la réussite ou l’échec du téléchargement.
   
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
10. Ajoutez la fonction suivante qui actualise l’état de mise à jour du microprogramme (en le définissant sur Application de l’image du microprogramme) via les propriétés signalées de la représentation.  Elle poursuit en simulant l’application de l’image du microprogramme, puis actualise l’état de mise à jour du microprogramme afin d’informer sur la réussite ou l’échec de l’application.
    
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
11. Ajoutez la fonction suivante qui gèrent la méthode firmwareUpdate et lance le processus en plusieurs étapes de mise à jour du microprogramme.
    
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
12. Enfin, ajoutez le code suivant qui connecte à IoT Hub en tant qu’appareil. 
    
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
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme suggéré dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].
> 
> 

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Déclencher une mise à jour du microprogramme à distance sur l’appareil à l’aide d’une méthode directe
Dans cette section, vous créez une application console Node.js qui lance une mise à jour du microprogramme à distance sur un appareil à l’aide d’une méthode directe, et utilise des requêtes de la représentation d’appareil pour obtenir régulièrement l’état de la mise à jour du microprogramme active sur cet appareil.

1. Créez un dossier vide nommé **triggerfwupdateondevice**.  Dans le dossier **triggerfwupdateondevice**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes.  Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **triggerfwupdateondevice**, exécutez la commande suivante pour installer les packages Kit de développement logiciel (SDK) pour appareils **azure-iothub** et **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iot-hub@dtpreview --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_getstarted_service.js** dans le dossier **triggerfwupdateondevice**.
4. Ajoutez les instructions ’require’ suivantes au début du fichier **dmpatterns_getstarted_service.js** :
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Ajoutez les déclarations de variable suivantes et remplacez les valeurs d’espace réservé :
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Ajoutez la fonction suivante pour rechercher et afficher la valeur de la propriété signalée firmwareUpdate.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Ajoutez la fonction suivante pour appeler la méthode firmwareUpdate afin de redémarrer l’appareil cible :
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Enfin, ajoutez la fonction suivante au code pour démarrer la séquence de mise à jour du microprogramme et commencer périodiquement à afficher les propriétés signalées de la représentation :
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Enregistrez et fermez le fichier **dmpatterns_fwupdate_service.js**.

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. À l’invite de commandes, dans le dossier **triggerfwupdateondevice**, exécutez la commande suivante pour déclencher le redémarrage à distance et interroger la représentation d’appareil pour déterminer le moment du dernier redémarrage.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Vous pouvez la réaction à la méthode directe en imprimant le message.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher une mise à jour du microprogramme à distance sur un appareil, et utilisé périodiquement les propriétés signalées de la représentation pour comprendre la progression du processus de mise à jour du microprogramme.  

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Oct16_HO2-->


