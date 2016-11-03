<properties
 pageTitle="Utiliser des méthodes directes | Microsoft Azure"
 description="Ce didacticiel explique comment utiliser des méthodes directes"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>


# <a name="tutorial-use-direct-methods"></a>Didacticiel : Utiliser des méthodes directes

## <a name="introduction"></a>Introduction

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Les didacticiels précédents ([Prise en main d’IoT Hub] et [Envoi de messages cloud-à-appareil avec IoT Hub]) illustre les fonctionnalités de base de messagerie appareil-à-cloud et cloud-à-appareil offertes par IoT Hub. IoT Hub permet également d’appeler des méthodes non durables sur des appareils à partir du cloud. Les méthodes représentent une interaction de demande-réponse avec un appareil, similaire à un appel HTTP, dans la mesure où elles réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur) pour permettre à l’utilisateur de connaître l’état de l’appel. [Appeler une méthode directe sur un appareil][lnk-devguide-méthodes] décrit plus en détail les méthodes, et indique quand utiliser des méthodes plutôt que des messages cloud-à-appareil.

Ce didacticiel vous explique les procédures suivantes :

- Utiliser le portail Azure pour créer un IoT Hub et une identité d’appareil dans celui-ci.
- Créer un appareil simulé disposant d’une méthode directe qui peut être appelée par le cloud.
- Créer une application console qui appelle une méthode directe sur l’appareil simulé via votre IoT Hub.

> [AZURE.NOTE] Actuellement, les méthodes directes sont accessibles uniquement à partir d’appareils qui se connectent à l’IoT Hub en utilisant le protocole MQTT. Pour obtenir des instructions sur la conversion d’une application d’appareil existante pour utiliser MQTT, voir [Support MQTT][lnk-devguide-mqtt].

À la fin de ce didacticiel, vous disposerez de deux applications console Node.js :

* **CallMethodOnDevice.js**, qui appelle une méthode sur l’appareil simulé et affiche la réponse ;
* **SimulatedDevice.js**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, puis répond à la méthode appelée par le cloud.

> [AZURE.NOTE] L’article [Kits de développement logiciel (SDK) IoT Hub][lnk-hub-sdks] fournit des informations sur les différents Kits de développement logiciel (SDK) que vous pouvez utiliser pour générer les deux applications à exécuter sur les appareils et votre serveur principal de solution.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

+ Node.js version 0.10.x ou ultérieure.

+ Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Version d’évaluation gratuite d’Azure][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode appelée par le cloud.

1. Créez un dossier vide appelé **simulateddevice**. Dans le dossier **simulateddevice** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À l’invite de commandes, dans le dossier **simulateddevice**, exécutez la commande suivante pour installer les packages Kit de développement logiciel (SDK) pour appareils **azure-iot-device** et **azure-iot-device-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
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

6. Ajoutez la fonction suivante pour implémenter la méthode sur l’appareil :

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

7. Ouvrez la connexion à votre IoT Hub et commencez à initialiser l’écouteur de la méthode :

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

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez mettre en œuvre des stratégies de nouvelle tentative (par exemple, une nouvelle tentative de connexion), comme suggéré dans l’article MSDN [Gestion des erreurs temporaires][lnk-transient-faults].

## <a name="call-a-method-on-a-device"></a>Appeler une méthode sur un appareil

Dans cette section, vous créez une application console Node.js qui appelle une méthode sur l’appareil simulé, puis affiche la réponse.

1. Créez un dossier vide nommé **callmethodondevice**. Dans le dossier **callmethodondevice**, créez un fichier package.json en utilisant la commande suivante à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À l’invite de commandes, dans le dossier **callmethodondevice**, exécutez la commande suivante pour installer le package **azure-iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **CallMethodOnDevice.js** dans le dossier **callmethodondevice**.

4. Ajoutez les instructions `require` ci-dessous au début du fichier **CallMethodOnDevice.js**:

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Ajoutez la déclaration de variable suivante et remplacez la valeur de l’espace réservé par la chaîne de connexion pour votre IoT Hub :

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. Créez le client pour ouvrir la connexion à votre IoT Hub.

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. Ajoutez la fonction suivante pour appeler la méthode d’appareil et imprimer la réponse de l’appareil vers la console :

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. Enregistrez et fermez le fichier **CallMethodOnDevice.js**.

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, dans le dossier **simulateddevice**, exécutez la commande suivante pour commencer à écouter les appels de méthode à partir de votre IoT Hub :

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. À l’invite de commandes, dans le dossier **callmethodondevice**, exécutez la commande suivante pour commencer à analyser votre IoT Hub :

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. Vous voyez l’appareil réagir à la méthode en imprimant le message, et l’application qui a appelé la méthode afficher la réponse de l’appareil :

    ![][9]
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez configuré un nouveau IoT Hub dans le portail, puis créé une identité d’appareil dans le registre d’identités du concentrateur. Vous avez utilisé cette identité d’appareil pour permettre à l’application d’appareil simulé de réagir à des méthodes appelées par le cloud. Vous avez également créé une application qui appelle des méthodes sur l’appareil et affiche la réponse de celui-ci. 

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

- [Prise en main d’IoT Hub]
- [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Pour savoir comment étendre votre solution IoT et planifier des appels de méthode sur plusieurs appareils, voir le didacticiel [Planifier et diffuser des travaux][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Envoi de messages cloud à appareil avec IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Traitement des messages appareil à cloud]: iot-hub-csharp-csharp-process-d2c.md
[Prise en main d’IoT Hub]: iot-hub-node-node-getstarted.md



<!--HONumber=Oct16_HO2-->


