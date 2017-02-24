---
title: "Simulation d’un appareil avec le Kit de développement logiciel (SDK) de la passerelle Azure IoT (Linux) | Microsoft Docs"
description: "Comment utiliser le Kit de développement logiciel (SDK) de la passerelle Azure IoT sous Linux pour créer un appareil simulé qui envoie les données de télémétrie à un IoT Hub via une passerelle."
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
ms.date: 02/08/2017
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 5edf2f4c7d9d2e8e8ceb2e8de9ae7cef4c9fd02e
ms.openlocfilehash: f6e3d0bfd45cb5cd133d77bcb23113c3f419450c


---
# <a name="use-the-azure-iot-gateway-sdk-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Utilisation du kit de développement logiciel (SDK) de la passerelle Azure IoT pour envoyer des messages appareil-à-cloud avec un appareil simulé (Linux)
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.
Avant de commencer, vous devez :

* [Configurer votre environnement de développement][lnk-setupdevbox] pour utiliser le Kit de développement logiciel (SDK) sous Linux.
* [Créez un IoT Hub][lnk-create-hub] dans votre abonnement Azure (vous aurez besoin du nom de votre hub pour effectuer cette procédure pas à pas). Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* Ajoutez deux appareils à votre hub IoT et notez leur ID et leurs clés d'appareil. Vous pouvez utiliser l’outil [Explorateur d’appareils][lnk-device-explorer] ou [iothub-explorer][lnk-iothub-explorer] pour ajouter vos appareils à l’IoT Hub que vous avez créé à l’étape précédente et récupérer ainsi leurs clés.

Pour créer l'exemple :

1. Ouvrez un interpréteur de commandes.
2. Accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** .
3. Exécutez le script **tools/build.sh** . Ce script utilise l’utilitaire **cmake** pour créer un dossier appelé **build** dans le dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** et pour générer un makefile. Le script crée ensuite la solution, et ignore les tests unitaires et les tests de bout en bout. Ajoutez le paramètre **--run-unittests** si vous souhaitez générer et exécuter les tests unitaires. Ajoutez le paramètre **--run-e2e-tests** si vous souhaitez générer et exécuter les tests de bout en bout. 

> [!NOTE]
> Chaque fois que vous exécutez le script **build.sh**, celui-ci supprime et recrée le dossier **build** dans le dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.
> 
> 

Pour exécuter l'exemple :

Dans un éditeur de texte, ouvrez le fichier **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** dans votre copie locale du référentiel **azure-iot-gateway-sdk**. Ce fichier configure les modules dans l'exemple de passerelle :

* Le module **IoTHub** se connecte à votre hub IoT. Vous devez le configurer pour envoyer des données à votre hub IoT. Plus précisément, définissez la valeur **IoTHubName** sur le nom de votre IoT Hub et la valeur **IoTHubSuffix** sur **azure-devices.net**. Définissez la valeur **Transport** sur « HTTP », « AMQP » ou « MQTT ». Notez que pour le moment, seul « HTTP » partage une connexion TCP pour tous les messages d’appareils. Si vous définissez la valeur sur « AMQP » ou « MQTT », la passerelle gère une connexion TCP à IoT Hub distincte pour chaque appareil.
* Le module **mapping** mappe les adresses MAC des appareils simulés aux ID de vos appareils IoT Hub. Assurez-vous que les valeurs **deviceId** correspondent aux ID des deux appareils que vous avez ajoutés à votre IoT Hub et que les valeurs **deviceKey** contiennent les clés de vos deux appareils.
* Les modules **BLE1** et **BLE2** sont les appareils simulés. Notez la façon dont leurs adresses MAC correspondent à celles du module **mapping** .
* Le module **Logger** consigne l’activité de votre passerelle dans un fichier.
* Les valeurs **module path** affichées ci-dessous supposent que vous exécuterez l’exemple à partir de la racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.
* Le tableau **links** à la fin du fichier JSON se connecte les modules **BLE1** et **BLE2** au module **mapping**, et le module **mapping** au module **IoTHub**. Il garantit également que tous les messages sont consignés par le module **Logger** .

```
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

1. Dans votre interpréteur de commandes, accédez au dossier **azure-iot-gateway-sdk/build** .
2. Exécutez la commande suivante :
   
    ```
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. Vous pouvez utiliser l’outil [Explorateur d’appareils][lnk-device-explorer] ou [iothub-explorer][lnk-iothub-explorer] pour analyser les messages qu’IoT Hub reçoit de la passerelle.

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez approfondir vos connaissances sur le Kit de développement logiciel (SDK) de passerelle IoT et découvrir certains exemples de code, consultez les didacticiels de développement et les ressources suivants :

* [Envoi de messages appareil-à-cloud à partir d’un appareil physique avec le Kit de développement logiciel (SDK) de la passerelle IoT][lnk-physical-device]
* [Kit de développement logiciel (SDK) de la passerelle Azure IoT][lnk-gateway-sdk]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Sécuriser votre solution IoT de bout en bout][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md



<!--HONumber=Feb17_HO1-->


