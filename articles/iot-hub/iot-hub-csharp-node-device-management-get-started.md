---
title: Prise en main de la gestion des appareils Azure IoT Hub (.NET/Node) | Microsoft Docs
description: "Guide d’utilisation de la gestion des appareils Azure IoT Hub pour lancer un redémarrage d’appareil à distance. Vous utilisez Azure IoT device SDK pour Node.js afin d’implémenter une application d’appareil simulé qui inclut une méthode directe et Azure IoT service SDK pour .NET afin d’implémenter une application de service qui appelle la méthode directe."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 0a4dc9c39d864dcfd52121ed4e4fa2393b56f872
ms.lasthandoff: 03/06/2017


---
# <a name="get-started-with-device-management-netnode"></a>Prise en main de la gestion d’appareils (.NET/Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]
## <a name="introduction"></a>Introduction
Les applications principales peuvent utiliser des primitives dans Azure IoT Hub, à savoir la représentation d’appareil et les méthodes directes, pour démarrer et surveiller à distance les actions de gestion sur les appareils.  Cet article fournit des conseils et du code illustrant comment les applications principales et les appareils fonctionnent ensemble pour lancer et surveiller le redémarrage d’un appareil distant à l’aide d’IoT Hub.

Pour démarrer et surveiller à distance les actions de gestion sur vos appareils à partir d’une application cloud principale, utilisez des primitives IoT Hub comme la [représentation d’appareil][lnk-devtwin] et les [méthodes directes][lnk-c2dmethod]. Ce didacticiel illustre comment une application principale et un appareil peuvent fonctionner ensemble pour vous permettre de lancer et de surveiller le redémarrage d’un appareil distant à partir d’IoT Hub.

Vous utilisez une méthode directe pour lancer des actions de gestion d’appareils (redémarrage, réinitialisation des paramètres d’usine et mise à jour du microprogramme) à partir d’une application principale dans le cloud. L’appareil est chargé de :

* Gérer la requête de méthode envoyée à partir d’IoT Hub.
* Démarrer l’action correspondante spécifique à l’appareil sur l’appareil.
* Fournir à IoT Hub des mises à jour de l’état via les propriétés signalées.

Vous pouvez utiliser une application principale dans le cloud pour exécuter des requêtes sur la représentation d’appareil afin d’indiquer la progression des actions de gestion de votre appareil.

Ce didacticiel vous explique les procédures suivantes :

* Utiliser le portail Azure pour créer un IoT Hub et une identité d’appareil dans celui-ci.
* Créer une application d’appareil simulé disposant d’une méthode directe permettant le redémarrage qui peut être appelée par le cloud.
* Créer une application console .NET qui appelle une méthode directe de redémarrage sur l’application d’appareil simulé via votre IoT Hub.

À la fin de ce didacticiel, vous avez une application d’appareil de console Node.js et une application principale de console .NET (c#) :

**dmpatterns_getstarted_device.js**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, reçoit une méthode directe de redémarrage, simule un redémarrage physique et indique l’heure du dernier redémarrage.

**TriggerReboot**, qui appelle une méthode directe sur l’application d’appareil simulé, affiche la réponse et les propriétés signalées mises à jour.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio 2015 ou Visual Studio 2017.
* Node.js version 0.12.x ou version ultérieure. <br/>  L’article [Préparer votre environnement de développement][lnk-dev-setup] décrit l’installation de Node.js pour ce didacticiel sur Windows ou sur Linux.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Déclencher un redémarrage à distance sur l’appareil à l’aide d’une méthode directe
Dans cette section, vous créez une application console .NET (à l’aide de C#) qui lance un redémarrage à distance sur un appareil à l’aide d’une méthode directe, et utilise des requêtes de la représentation d’appareil pour déterminer l’heure du dernier redémarrage de cet appareil.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à une nouvelle solution en utilisant le modèle de projet **Application console (.NET Framework)**. Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **TriggerReboot**.

    ![Nouveau projet Visual C# Bureau classique Windows][img-createapp]

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **TriggerReboot**, puis cliquez sur **Gérer les packages NuGet**.
3. Dans la fenêtre **Gestionnaire de package NuGet**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement et l’installation et ajoute une référence au [package Azure IoT Service SDK NuGet][lnk-nuget-service-sdk] et ses dépendances.

    ![Fenêtre du gestionnaire de package NuGet][img-servicenuget]
4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using Microsoft.Azure.Devices;
        
5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion pour le IoT Hub créé dans la section précédente et l’appareil cible.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static JobClient jobClient;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
6. Ajoutez la méthode suivante à la classe **Program**.  Ce code obtient la représentation d’appareil pour le redémarrage de l’appareil et renvoie les propriétés signalées.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Ajoutez la méthode suivante à la classe **Program**.  Ce code lance un redémarrage à distance sur l’appareil à l’aide d’une méthode directe.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Générez la solution.

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous allez :

* Créer une application console Node.js qui répond à une méthode directe appelée par le cloud
* Déclencher un redémarrage d’appareil simulé
* Utiliser les propriétés signalées pour activer les requêtes sur la représentation d’appareil afin d’identifier les appareils et l’heure de leur dernier redémarrage

1. Créez un dossier vide nommé **simulateddevice**.  Dans le dossier **simulateddevice**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes.  Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour installer les packages Kit de développement logiciel (SDK) pour appareils **azure-iot-device** et **azure-iot-device-mqtt** :
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. À l’aide d’un éditeur de texte, créez un fichier **dmpatterns_getstarted_device.js** dans le dossier **manageddevice**.
4. Ajoutez les instructions 'require' suivantes au début du fichier **dmpatterns_getstarted_device.js** :
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Ajoutez une variable **connectionString** et utilisez-la pour créer une instance de **Client**.  Remplacez la chaîne de connexion par la chaîne de connexion de votre appareil.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Ajoutez la fonction suivante pour implémenter la méthode directe sur l’appareil
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Ajoutez le code suivant pour ouvrir la connexion à votre IoT Hub et démarrez l’écouteur de la méthode directe :
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Enregistrez et fermez le fichier **dmpatterns_getstarted_device.js**.
   
   [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].


## <a name="run-the-apps"></a>Exécuter les applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **manageddevice**, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. Exécutez l’application console C# **TriggerReboot**, cliquez avec le bouton droit sur le projet **TriggerReboot**, puis sélectionnez **Debug** et **Démarrer une nouvelle instance**.

3. La réponse de l’appareil à la méthode directe s’affiche dans la console.

## <a name="customize-and-extend-the-device-management-actions"></a>Personnaliser et étendre les actions de gestion d’appareils
Vos solutions IoT peuvent étendre l’ensemble défini de modèles de gestion d’appareils ou activer des modèles personnalisés en utilisant les primitives de la méthode cloud-à-appareil et de la représentation d’appareil. La réinitialisation des paramètres d’usine, la mise à jour du microprogramme, la mise à jour logicielle, la gestion de l’alimentation, la gestion du réseau et de la connectivité, et le chiffrement des données sont d’autres exemples d’actions de gestion des appareils.

## <a name="device-maintenance-windows"></a>Fenêtres de maintenance d’appareil
En règle générale, vous configurez des appareils pour effectuer des actions à un moment qui minimise les interruptions et les temps d’arrêt.  Les fenêtres de maintenance d’appareil constituent un modèle couramment utilisé pour définir l’heure à laquelle un appareil doit mettre à jour sa configuration. Vos solutions principales peuvent utiliser les propriétés souhaitées de la représentation d’appareil pour définir et activer une stratégie sur votre appareil qui permet d’obtenir une fenêtre de maintenance. Lorsqu’un appareil reçoit la stratégie de fenêtre de maintenance, il peut utiliser la propriété signalée de la représentation d’appareil pour indiquer l’état de la stratégie. L’application principale peut ensuite utiliser des requêtes de représentation d’appareil pour certifier la conformité des appareils et de chaque stratégie.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher un redémarrage à distance sur un appareil, vous avez utilisé les propriétés signalées pour indiquer le moment du dernier redémarrage de l’appareil et vous avez interrogé la représentation d’appareil pour découvrir l’heure du dernier redémarrage de l’appareil à partir du cloud.

Pour approfondir la prise en main d’IoT Hub et des modèles de gestion d’appareils, comme la mise à jour du microprogramme à distance, consultez :

[Didacticiel : Mettre à jour un microprogramme][lnk-fwupdate]

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

Pour approfondir la prise en main de IoT Hub, consultez l’article [Prise en main du Kit de développement logiciel (SDK) IoT Gateway][lnk-gateway-SDK].

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-node-device-management-get-started/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-device-management-get-started/createnetapp.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
