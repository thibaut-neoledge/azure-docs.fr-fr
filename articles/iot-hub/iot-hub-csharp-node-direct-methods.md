---
title: "Utilisez des méthodes directes Azure IoT Hub (C#) | Microsoft Docs"
description: "Ce didacticiel explique comment utiliser des méthodes directes"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: f2af421140f4f6640578a372993c2553f8c590a4


---
# <a name="tutorial-use-direct-methods-c"></a>Didacticiel : Utiliser des méthodes directes (C#)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

À la fin de ce didacticiel, vous disposerez d’applications console Node.js et .NET :

* **CallMethodOnDevice.js**, application .NET conçue pour s’exécuter depuis le serveur principal, qui appelle une méthode dans l’application d’appareil simulé et affiche la réponse.
* **SimulatedDevice.js**, application Node.js qui simule un appareil se connectant à votre IoT Hub avec l’identité d’appareil créée précédemment, et répond à la méthode appelée par le cloud.

> [!NOTE]
> L’article [Kits de développement logiciel (SDK) Azure IoT][lnk-hub-sdks] fournit des informations sur les kits de développement logiciel Azure IoT que vous pouvez utiliser pour générer les deux applications qui s’exécutent sur les appareils et sur le serveur de solution principal.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Microsoft Visual Studio 2015.
* Node.js version 0.10.x ou ultérieure.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé
Dans cette section, vous créez une application console Node.js qui répond à une méthode appelée par le serveur principal.

1. Créez un dossier vide appelé **simulateddevice**. Dans le dossier **simulateddevice** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :
   
    ```
    npm init
    ```
2. À l’invite de commandes, dans le dossier **simulateddevice**, exécutez la commande suivante pour installer les packages **azure-iot-device** et **azure-iot-device-mqtt** :
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Dans un éditeur de texte, créez un fichier **SimulatedDevice.js** dans le dossier **simulateddevice**.
4. Ajoutez les instructions `require` ci-dessous au début du fichier **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Ajoutez une variable **connectionString** et utilisez-la pour créer un client d’appareil. Remplacez **{device connection string}** par la chaîne connexion que vous générée dans la section *Créer une identité d’appareil* :
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Ajoutez la fonction suivante pour implémenter la méthode directe sur l’appareil :
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Ouvrez la connexion à votre IoT Hub et initialisez l’écouteur de la méthode :
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Enregistrez et fermez le fichier **SimulatedDevice.js** .

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une nouvelle tentative de connexion), comme suggéré dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>Appeler une méthode directe sur un appareil
Dans cette section, vous créez une application console .NET qui appelle une méthode sur l’application d’appareil simulé, puis affiche la réponse.

1. Dans Visual Studio, ajoutez un projet Visual C# Bureau classique Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Assurez-vous que la version du .NET Framework est définie sur 4.5.1 ou supérieur. Nommez le projet **CallMethodOnDevice**.
   
    ![Nouveau projet Visual C# Bureau classique Windows][10]
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **CallMethodOnDevice**, puis cliquez sur **Gérer les packages Nuget**.
3. Dans la fenêtre **Gestionnaire de package Nuget**, cliquez sur **Parcourir**, puis recherchez **microsoft.azure.devices**. Cliquez ensuite sur **Installer** pour installer le package **Microsoft.Azure.Devices**, puis acceptez les conditions d’utilisation. Cette procédure lance le téléchargement, l’installation et ajoute une référence au package Nuget [kit de développement logiciel (SDK) de service Microsoft Azure IoT][lnk-nuget-service-sdk] et ses dépendances.
   
    ![Fenêtre du gestionnaire de package Nuget][11]

4. Ajoutez les instructions `using` suivantes en haut du fichier **Program.cs** :
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion pour le IoT Hub créé dans la section précédente.
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Ajoutez la méthode suivante à la classe **Program** :
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    Cette méthode appelle une méthode directe avec le nom `writeLine` sur l’appareil `myDeviceId`. Ensuite, elle écrit la réponse fournie par l’appareil sur la console. Notez qu’il est possible de spécifier une valeur de délai d’attente pour la réponse de l’appareil.
7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **simulateddevice**, exécutez la commande suivante pour commencer à écouter les appels de méthode à partir de votre IoT Hub :
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. Maintenant que l’appareil est connecté et en attente d’appels de méthode, exécutez l’application .NET **CallMethodOnDevice** pour appeler la méthode dans l’application pour appareil simulé. Vous devriez voir la réponse de l’appareil écrite dans la console.
   
    ![][8]
3. Vous voyez l’appareil réagir à la méthode en imprimant le message, et l’application qui a appelé la méthode afficher la réponse de l’appareil :
   
    ![][9]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail Azure, puis créé une identité d’appareil dans le registre des identités de l’IoT Hub. Vous avez utilisé cette identité d’appareil pour permettre à l’application d’appareil simulé de réagir à des méthodes appelées par le cloud. Vous avez également créé une application qui appelle des méthodes sur l’appareil et affiche la réponse de celui-ci. 

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

* [Prise en main d’IoT Hub]
* [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/create-identity-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Prise en main d’IoT Hub]: iot-hub-node-node-getstarted.md



<!--HONumber=Nov16_HO5-->


