---
title: Simuler un appareil avec Azure IoT Edge (Windows) | Documents Microsoft
description: "Comment utiliser le Kit de développement logiciel (SDK) de la passerelle Azure IoT sous Windows pour créer un appareil simulé qui envoie les données de télémétrie à un Azure IoT Hub via une passerelle."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 11e7bf28-ee3d-48d6-a386-eb506c7a31cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/09/2017
ms.author: andbuc
ms.openlocfilehash: 5349960373ae6815862c5f79a69dd6d5d9d624ab
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Utilisation d’Azure IoT Edge pour envoyer des messages appareil-à-cloud avec un appareil simulé (Linux)

[!INCLUDE [iot-hub-iot-edge-simulated-selector](../../includes/iot-hub-iot-edge-simulated-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>Comment exécuter l’exemple

Le script **build.sh** génère sa sortie dans le dossier **build** de votre copie locale du référentiel **iot-edge**. Cette sortie inclut les quatre modules IoT Edge utilisés dans cet exemple.

Le script build place les fichiers comme suit :

* **liblogger.so** dans le dossier **build/modules/logger**.
* **libiothub.so** dans le dossier **build/modules/iothub**.
* **lib\_identity\_map.so** dans le dossier **build/modules/identitymap**.
* **libsimulated\_device.so** dans le dossier **build/modules/simulated\_device**.

Utilisez ces chemins pour les valeurs **module path** comme indiqué dans le fichier de paramètres JSON suivant :

Le processus simulated\_device\_cloud\_upload\_sample utilise le chemin vers un fichier de configuration JSON comme argument de ligne de commande. L’exemple de fichier JSON suivant est fourni dans le référentiel du Kit de développement logiciel, sous **samples\\simulated\_device\_cloud\_upload\_sample\\src\\simulated\_device\_cloud\_upload\_sample\_lin.json**. Ce fichier de configuration fonctionne comme tel, sauf si vous modifiez le script build pour placer des modules IoT Edge ou des exécutables de l’exemple dans des emplacements autres que ceux par défaut.

> [!NOTE]
> Les chemins d’accès du module sont relatifs au répertoire à partir duquel vous exécutez l’exécutable simulated\_device\_cloud\_upload\_sample, et non au répertoire où se trouve l’exécutable. L’exemple de fichier de configuration JSON écrit par défaut dans « deviceCloudUploadGatewaylog.log » dans votre répertoire de travail actuel.

Dans un éditeur de texte, ouvrez le fichier **samples/simulated\_device\_cloud\_upload\_sample/src/simulated\_device\_cloud\_upload\_lin.json** dans votre copie locale du référentiel **iot-edge**. Ce fichier configure les modules IoT Edge dans l'exemple de passerelle :

* Le module **IoTHub** se connecte à votre hub IoT. Vous le configurez pour envoyer des données à votre hub IoT. Plus précisément, définissez la valeur **IoTHubName** sur le nom de votre IoT Hub et la valeur **IoTHubSuffix** sur **azure-devices.net**. Définissez la valeur de **Transport** sur **HTTP**, **AMQP** ou **MQTT**. Actuellement, seul **HTTP** partage une connexion TCP pour tous les messages d’appareils. Si vous définissez la valeur sur **AMQP** ou **MQTT**, la passerelle gère une connexion TCP à IoT Hub distincte pour chaque appareil.
* Le module **mapping** mappe les adresses MAC des appareils simulés aux ID de vos appareils IoT Hub. Assurez-vous que les valeurs **deviceId** correspondent aux ID des deux appareils que vous avez ajoutés à votre IoT Hub et que les valeurs **deviceKey** contiennent les clés de vos deux appareils.
* Les modules **BLE1** et **BLE2** sont les appareils simulés. Notez comment leurs adresses MAC correspondent à celles du module **mapping**.
* Le module **Logger** consigne l’activité de votre passerelle dans un fichier.
* Les valeurs **module path** affichées dans l’exemple supposent que vous exécuterez l’exemple à partir du dossier **build** de votre copie locale du référentiel **iot-edge**.
* Le tableau **links** à la fin du fichier JSON se connecte les modules **BLE1** et **BLE2** au module **mapping**, et le module **mapping** au module **IoTHub**. Il garantit également que tous les messages sont consignés par le module **Logger** .

```json
{
    "modules": [
        {
            "name": "IotHub",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/iothub/libiothub.so"
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
              "module.path": "./modules/identitymap/libidentity_map.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args": {
              "filename": "deviceCloudUploadGatewaylog.log"
            }
          }
    ],
    "links": [
        {
            "source": "*",
            "sink": "Logger"
        },
        {
            "source": "BLE1",
            "sink": "mapping"
        },
        {
            "source": "BLE2",
            "sink": "mapping"
        },
        {
            "source": "mapping",
            "sink": "IotHub"
        }
    ]
}
```

Enregistrez les modifications apportées au fichier de configuration.

Pour exécuter l'exemple :

1. Dans votre interpréteur de commandes, accédez au dossier **iot-edge/build**.
2. Exécutez la commande suivante :
   
    ```sh
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. Vous pouvez utiliser l’outil [Explorateur d’appareils][lnk-device-explorer] ou [iothub-explorer][lnk-iothub-explorer] pour analyser les messages qu’IoT Hub reçoit de la passerelle. Par exemple, à l’aide d’iothub-explorer, vous pouvez surveiller les messages appareil-à-cloud à l’aide de la commande suivante :

    ```sh
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
