---
title: "Simulation d’un appareil avec le Kit de développement logiciel (SDK) de passerelle IoT | Microsoft Docs"
description: "Procédure pas à pas du Kit de développement logiciel (SDK) de passerelle Azure IoT sous Windows pour illustrer l’envoi de données de télémétrie à partir d’un appareil simulé à l’aide du Kit de développement logiciel (SDK) de passerelle Azure IoT."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: f5eeea933dbd5b63c6a8f2bd5b065a13286a4bae


---
# <a name="azure-iot-gateway-sdk--send-device-to-cloud-messages-with-a-simulated-device-app-using-windows"></a>Kit de développement logiciel (SDK) de passerelle Azure IoT - Envoyer des messages appareil-à-cloud avec une application d’appareil simulé à l’aide de Windows
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.
Avant de commencer, vous devez :

* [Configurer votre environnement de développement][lnk-setupdevbox] pour utiliser le Kit de développement logiciel (SDK) sous Windows.
* [Créer un IoT Hub][lnk-create-hub] dans votre abonnement Azure (vous aurez besoin du nom de votre concentrateur pour effectuer cette procédure pas à pas). Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* Ajoutez deux appareils à votre hub IoT et notez leur ID et leurs clés d'appareil. Vous pouvez utiliser l’outil [Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour ajouter vos appareils à l’IoT Hub que vous avez créé à l'étape précédente et récupérer ainsi leurs clés.

Pour créer l'exemple :

1. Ouvrez une **invite de commandes Développeur pour VS2015** .
2. Accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** .
3. Exécutez le script **tools\\build.cmd**. Ce script crée un fichier de solution Visual Studio, génère la solution, puis exécute les tests. Vous trouverez la solution Visual Studio dans le dossier **build** de votre copie locale du référentiel **azure-iot-gateway-sdk**.

Pour exécuter l'exemple :

Dans un éditeur de texte, ouvrez le fichier **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** dans votre copie locale du référentiel **azure-iot-gateway-sdk**. Ce fichier configure les modules dans l'exemple de passerelle :

* Le module **IoTHub** se connecte à votre hub IoT. Vous devez le configurer pour envoyer des données à votre hub IoT. Plus précisément, définissez la valeur **IoTHubName** sur le nom de votre IoT Hub et la valeur **IoTHubSuffix** sur **azure-devices.net**. Définissez la valeur **Transport** sur « HTTP », « AMQP » ou « MQTT ». Notez que pour le moment, seul « HTTP » partage une connexion TCP pour tous les messages d’appareils. Si vous définissez la valeur sur « AMQP » ou « MQTT », la passerelle gère une connexion TCP à IoT Hub distincte pour chaque appareil.
* Le module **mapping** mappe les adresses MAC des appareils simulés aux ID de vos appareils IoT Hub. Assurez-vous que les valeurs **deviceId** correspondent aux ID des deux appareils que vous avez ajoutés à votre IoT Hub et que les valeurs **deviceKey** contiennent les clés de vos deux appareils.
* Les modules **BLE1** et **BLE2** sont les appareils simulés. Notez la façon dont leurs adresses MAC correspondent à celles du module **mapping** .
* Le module **Logger** consigne l’activité de votre passerelle dans un fichier.
* Les valeurs **module path** ci-dessous supposent que vous avez cloné le référentiel du SDK de passerelle IoT à la racine de votre lecteur **C:**. Si vous l’avez téléchargé vers un autre emplacement, vous devez modifier en conséquence les valeurs **module path** .
* Le tableau **links** à la fin du fichier JSON se connecte les modules **BLE1** et **BLE2** au module **mapping**, et le module **mapping** au module **IoTHub**. Il garantit également que tous les messages sont consignés par le module **Logger** .

```
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01"
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02"
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

Enregistrez les modifications apportées au fichier de configuration.

Pour exécuter l'exemple :

1. Dans une invite de commande, accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** .
2. Exécutez la commande suivante :
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. Vous pouvez utiliser l’outil [Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour analyser les messages qu’IoT Hub reçoit de la passerelle.

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez approfondir vos connaissances sur le Kit de développement logiciel (SDK) de passerelle IoT et découvrir certains exemples de code, consultez les didacticiels de développement et les ressources suivants :

* [Envoi de messages appareil-à-cloud depuis un appareil physique avec le Kit de développement logiciel (SDK) de passerelle IoT][lnk-physical-device]
* [Kit de développement logiciel (SDK) de la passerelle Azure IoT][lnk-gateway-sdk]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Nov16_HO5-->


