## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous allez :

* Créer une application console Node.js qui répond à une méthode directe appelée par le cloud
* Déclencher une mise à jour du microprogramme simulé
* Utiliser les propriétés signalées pour activer les requêtes sur la représentation d’appareil afin d’identifier les appareils et l’heure de leur dernière mise à jour de microprogramme

Étape 1 : Créez un dossier vide nommé **manageddevice**.  Dans le dossier **simulateddevice**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```

Étape 2 : À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour installer les packages Kit de développement logiciel (SDK) pour appareils **azure-iot-device** et **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

Étape 3 : À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_fwupdate_device.js** dans le dossier **manageddevice**.

Étape 4 : Ajoutez les instructions « require » suivantes au début du fichier **dmpatterns_fwupdate_device.js** :
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
Étape 5 : Ajoutez une variable **connectionString** et utilisez-la pour créer une instance de **Client**. Remplacez l’espace réservé `{yourdeviceconnectionstring}` par la chaîne de connexion que vous avez notée précédemment dans la section « Création d’une identité d’appareil » :
   
    ```
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

Étape 6 : Ajoutez la fonction suivante qui sera utilisée pour mettre à jour les propriétés signalées :
   
    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };
   
      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported: ' + firmwareUpdateValue.status);
      });
    };
    ```

Étape 7 : Ajoutez les fonctions suivantes qui simulent le téléchargement et l’application de l’image du microprogramme :
   
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

Étape 8 : Ajoutez la fonction suivante qui actualise l’état de mise à jour du microprogramme via les propriétés signalées en le définissant sur **waiting** (en attente). Généralement, un appareil est informé de la disponibilité d’une mise à jour, et une stratégie définie par un administrateur a pour effet que l’appareil commence à télécharger et à appliquer la mise à jour. C’est dans cette fonction qu’intervient la logique d’activation de cette stratégie. Pour plus de simplicité, l’exemple attend pendant quatre secondes avant le téléchargement de l’image de microprogramme :
   
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

Étape 9 : Ajoutez la fonction suivante qui actualise l’état de mise à jour du microprogramme via les propriétés signalées en le définissant sur **downloading** (téléchargement en cours). La fonction simule ensuite un téléchargement de microprogramme, puis actualise l’état de mise à jour du microprogramme sur **downloadFailed** (échec du téléchargement) ou **downloadComplete** (téléchargement terminé) :
   
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

Étape 10 : Ajoutez la fonction suivante qui actualise l’état de mise à jour du microprogramme via les propriétés signalées en le définissant sur **applying** (application en cours). La fonction simule ensuite l’application de l’image du microprogramme, puis actualise l’état de mise à jour du microprogramme sur **applyFailed** (échec de l’application) ou **applyComplete** (application terminée) :
    
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

Étape 11 : Ajoutez la fonction suivante qui gère la méthode directe **firmwareUpdate** et initie le processus en plusieurs étapes de mise à jour du microprogramme :
    
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
      var fwPackageUri = request.payload.fwPackageUri;
    
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

Étape 12 : Enfin, ajoutez le code suivant qui établit la connexion à votre IoT Hub :
    
    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
    
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate);
    });
    ```

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires](https://msdn.microsoft.com/library/hh675232.aspx).
> 
> 