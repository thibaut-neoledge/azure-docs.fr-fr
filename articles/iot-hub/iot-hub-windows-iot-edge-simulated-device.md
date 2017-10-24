---
title: Simuler un appareil avec Azure IoT Edge (Windows) | Microsoft Docs
description: "Comment utiliser Azure IoT Edge sous Windows pour créer un appareil simulé qui envoie les données de télémétrie à un IoT Hub via une passerelle IoT Edge."
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
ms.date: 09/19/2017
ms.author: andbuc
ms.openlocfilehash: 0aa1836ee1445894022b95fefc2338ef53698240
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Utilisation d’Azure IoT Edge pour envoyer des messages appareil-à-cloud avec un appareil simulé (Windows)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>Exécution de l'exemple

Le script **build.cmd** génère sa sortie dans le dossier **build** de votre copie locale du référentiel **iot-edge**. Cette sortie inclut les quatre modules IoT Edge utilisés dans cet exemple.

Le script de génération crée les fichiers suivants :

* **logger.dll** dans le dossier **build\\modules\\logger\\Debug**.
* **iothub.dll** dans le dossier **build\\modules\\iothub\\Debug**.
* **identity\_map.dll** dans le dossier **build\\modules\\identitymap\\Debug**.
* **simulated\_device.dll** dans le dossier **build\\modules\\simulated\_device\\Debug**.

Utilisez ces chemins d’accès comme valeurs de **module path**, comme dans le fichier de paramètres JSON simulated\_device\_cloud\_upload\_win.

L’exemple de processus simulated\_device\_cloud\_upload prend en argument de ligne de commande le chemin d’accès d’un fichier de configuration JSON. L’exemple de fichier JSON suivant est fourni dans le référentiel du Kit de développement logiciel (SDK), sous **samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_win.json**. Ce fichier config fonctionne comme tel, sauf si vous modifiez le script de build pour placer les modules IoT Edge ou des exemples d’exécutables dans des emplacements autres que ceux par défaut.

> [!NOTE]
> Les chemins de module sont relatifs au répertoire où se trouve le fichier simulated\_device\_cloud\_upload\_sample.exe. L’exemple de fichier de configuration JSON écrit par défaut dans « deviceCloudUploadGatewaylog.log » dans votre répertoire de travail actuel.

Dans un éditeur de texte, ouvrez le fichier **samples\\simulated\_device\_cloud\_upload\\src\\simulated\_device\_cloud\_upload\_win.json** dans votre copie locale du référentiel **iot-edge**. Ce fichier configure les modules IoT Edge dans l'exemple de passerelle :

* Le module **IoTHub** se connecte à votre hub IoT. Vous le configurez pour envoyer des données à votre hub IoT. Plus précisément, définissez la valeur **IoTHubName** sur le nom de votre IoT Hub et la valeur **IoTHubSuffix** sur **azure-devices.net**. Définissez la valeur de **Transport** sur **HTTP**, **AMQP** ou **MQTT**. Actuellement, seul **HTTP** partage une connexion TCP pour tous les messages d’appareils. Si vous définissez la valeur sur **AMQP** ou **MQTT**, la passerelle gère une connexion TCP à IoT Hub distincte pour chaque appareil.
* Le module **mapping** mappe les adresses MAC de vos appareils simulés avec les ID de vos appareils IoT Hub. Pour **deviceId**, entrez l’ID des deux appareils que vous avez ajoutés à votre hub IoT. Pour **deviceKey**, entrez la clé de vos deux appareils.
* Les modules **BLE1** et **BLE2** sont les appareils simulés. Notez comment les adresses MAC du module correspondent à celles du module **mapping**.
* Le module **Logger** consigne l’activité de votre passerelle dans un fichier.
* Les valeurs **module path** figurant dans l’exemple suivant sont relatives au répertoire où se trouve le fichier simulated\_device\_cloud\_upload\_sample.exe.
* Le tableau **links** à la fin du fichier JSON se connecte les modules **BLE1** et **BLE2** au module **mapping**, et le module **mapping** au module **IoTHub**. Il garantit également que tous les messages sont consignés par le module **Logger** .

```json
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
            "macAddress": "01:01:01:01:01:01",
            "messagePeriod" : 2000
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
            "macAddress": "02:02:02:02:02:02",
            "messagePeriod" : 2000
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

1. Accédez au dossier **build** de votre copie locale du référentiel **iot-edge**.
2. Exécutez la commande suivante :
   
    ```cmd
    samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe ..\samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. Vous pouvez utiliser l’outil [Explorateur d’appareils][lnk-device-explorer] ou [iothub-explorer][lnk-iothub-explorer] pour analyser les messages qu’IoT Hub reçoit de la passerelle. Par exemple, à l’aide d’iothub-explorer, vous pouvez surveiller les messages appareil-à-cloud à l’aide de la commande suivante :

    ```cmd
    iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour approfondir vos connaissances sur Azure IoT Edge et découvrir certains exemples de code, consultez les didacticiels de développement et les ressources suivants :

* [Envoi de messages appareil-à-cloud à partir d’un appareil physique avec Azure IoT Edge][lnk-physical-device]
* [Azure IoT Edge][lnk-iot-edge]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Sécuriser votre solution IoT de bout en bout][lnk-securing]

<!-- Links -->
[lnk-iot-edge]: https://github.com/Azure/iot-edge/
[lnk-physical-device]: iot-hub-iot-edge-physical-device.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
