---
title: "Utilisation d’un appareil physique avec le Kit de développement logiciel (SDK) de passerelle IoT | Microsoft Docs"
description: "Procédure pas à pas du Kit de développement logiciel (SDK) de passerelle Azure IoT utilisant un appareil SensorTag de Texas Instruments pour envoyer des données à un IoT Hub via une passerelle s’exécutant sur un Raspberry Pi 3"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: 9c8ab5b54644c3fa7999e7250825fba5d8532082


---
# <a name="azure-iot-gateway-sdk--send-device-to-cloud-messages-with-a-physical-device-using-linux"></a>Kit de développement logiciel (SDK) de passerelle Azure IoT (version bêta) - envoyer des messages appareil-à-cloud avec un appareil réel utilisant Linux
Cette procédure pas à pas sur l’[exemple de Bluetooth à faible consommation d’énergie][lnk-ble-samplecode] montre comment utiliser le [Kit de développement logiciel (SDK) de passerelle Azure IoT][lnk-sdk] pour transférer des données de télémétrie d’appareil-à-cloud vers un IoT Hub à partir d’un appareil physique, et comment acheminer des commandes d’un IoT Hub vers un appareil physique.

Cette procédure pas à pas inclut les étapes suivantes :

* **Architecture**: informations architecturales importantes concernant l'exemple à faible consommation d’énergie Bluetooth.
* **Créer et exécuter**: les étapes requises pour créer et exécuter l’exemple.

## <a name="architecture"></a>Architecture
La procédure pas à pas montre comment générer et exécuter une passerelle IoT sur un Raspberry Pi  3 exécutant Raspbian Linux. La passerelle est construite à l’aide du Kit de développement logiciel (SDK) de passerelle IoT. L’exemple utilise un appareil Texas Instruments SensorTag Bluetooth Low Energy (BLE) pour collecter des données de température.

Lorsque vous exécutez la passerelle, celle-ci :

* Se connecte à un appareil SensorTag à l’aide du protocole Bluetooth Low Energy (BLE).
* Se connecte à IoT Hub à l’aide du protocole HTTP.
* Transfère les données de télémétrie à partir de l’appareil SensorTag vers IoT Hub.
* Achemine les commandes vers l’appareil SensorTag à partir d’IoT Hub.

La passerelle contient les modules suivants :

* Un *module BLE* qui interagit avec un appareil BLE pour recevoir les données de température à partir de l’appareil et envoyer des commandes à l’appareil.
* Un *module cloud-à-appareil BLE* qui traduit les messages JSON provenant du cloud en instructions BLE pour le *module BLE*.
* Un *module enregistreur* qui journalise tous les messages de la passerelle dans un fichier local.
* Un *module de mappage d’identité* qui effectue la traduction entre les adresses MAC de l’appareil BLE et les identités des appareils Azure IoT Hub.
* Un *module IoT Hub* qui transfère les données de télémétrie sur un hub IoT et reçoit les commandes de l’appareil à partir d’un hub IoT.
* Un *module d’imprimante BLE* qui interprète les données de télémétrie fournies par l’appareil BLE et imprime des données mises en forme sur la console pour permettre la résolution des problèmes et le débogage.

### <a name="how-data-flows-through-the-gateway"></a>Flux des données sur la passerelle
Le diagramme de blocs suivant illustre le pipeline du flux de téléchargement des données de télémétrie :

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Les étapes qu’un élément de télémétrie suit lors de son transfert entre un appareil BLE et IoT Hub sont les suivantes :

1. L’appareil BLE génère un exemple de température et l’envoie au module BLE de la passerelle via Bluetooth.
2. Le module BLE reçoit l’exemple et le publie dans le répartiteur avec l’adresse MAC de l’appareil.
3. Le module de mappage d’identité récupère ce message et utilise une table interne pour convertir l’adresse MAC de l’appareil en une identité d’appareil IoT Hub (un ID d’appareil et une clé d’appareil). Il publie ensuite un nouveau message contenant les exemples de données de température, l’adresse MAC de l’appareil, l’ID de l’appareil et la clé de l’appareil.
4. Le module IoT Hub reçoit ce nouveau message (généré par le module de mappage d’identité) et le publie dans IoT Hub.
5. Le module enregistreur journalise tous les messages reçus du répartiteur dans un fichier local.

Le diagramme de blocs suivant illustre le pipeline du flux des données de commandes de l’appareil :

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Le module IoT Hub interroge régulièrement IoT Hub pour savoir s’il existe de nouveaux messages de commande.
2. Lorsque le module IoT Hub reçoit un nouveau message de commande, il le publie sur le répartiteur.
3. Le module de mappage d’identité récupère le message de commande et utilise une table interne pour convertir l’ID d’appareil IoT Hub en adresse MAC d’appareil. Il publie ensuite un nouveau message contenant l’adresse MAC de l’appareil cible dans le mappage des propriétés du message.
4. Le module cloud-à-appareil BLE récupère ce message et le traduit dans l’instruction BLE appropriée pour le module BLE. Il publie ensuite un nouveau message.
5. Le module BLE récupère ce message et exécute l’instruction d’E/S en communiquant avec l’appareil BLE.
6. Le module enregistreur consigne tous les messages reçus du répartiteur dans un fichier sur le disque.

## <a name="prepare-your-hardware"></a>Préparation du matériel
Ce didacticiel suppose que vous utilisez un appareil [SensorTag de Texas Instruments](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) connecté à un Raspberry Pi 3 exécutant Raspbian.

### <a name="install-raspbian"></a>Installer Raspbian
Vous pouvez utiliser l’une des options suivantes pour installer Raspbian sur votre appareil Raspberry Pi 3. 

* Utilisez [NOOBS][lnk-noobs], une interface utilisateur graphique, pour installer la dernière version de Raspbian. 
* [Téléchargez][lnk-raspbian] Manuellement et écrivez la dernière image du système d’exploitation Raspbian sur une carte SD. 

### <a name="install-bluez-537"></a>Installer BlueZ 5.37
Les modules BLE communiquent avec le matériel Bluetooth via la pile BlueZ. Pour que les modules fonctionnent correctement, vous avez besoin de la version 5.37 de BlueZ. En suivant ces instructions, vous êtes assuré que la version installée de BlueZ est correcte.

1. Arrêtez le démon Bluetooth actuel :
   
    ```
    sudo systemctl stop bluetooth
    ```
2. Installez les dépendances BlueZ. 
   
    ```
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```
3. Téléchargez le code source de BlueZ à partir de bluez.org. 
   
    ```
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```
4. Décompressez le code source.
   
    ```
    tar -xvf bluez-5.37.tar.xz
    ```
5. Déplacer les répertoires vers le dossier nouvellement créé.
   
    ```
    cd bluez-5.37
    ```
6. Configurez le code BlueZ à générer.
   
    ```
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```
7. Générez BlueZ.
   
    ```
    make
    ```
8. Installez BlueZ une fois la génération terminée.
   
    ```
    sudo make install
    ```
9. Modifiez la configuration du service systemd pour Bluetooth afin qu’il pointe vers le nouveau démon Bluetooth dans le fichier `/lib/systemd/system/bluetooth.service`. Remplacez la ligne « ExecStart » par le texte suivant : 
    
    ```
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Activer la connectivité à l’appareil SensorTag à partir de votre appareil Raspberry Pi 3
Avant d’exécuter l’exemple, vous devez vérifier que votre Raspberry Pi 3 peut se connecter à l’appareil SensorTag.


1. Vérifiez que l’utilitaire `rfkill` est installé.
   
    ```
    sudo apt-get install rfkill
    ```
2. Débloquez Bluetooth sur l’appareil Raspberry Pi 3, et vérifiez que le numéro de version est **5.37**.
   
    ```
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```
3. Démarrez le service Bluetooth et exécutez la commande **bluetoothctl** pour entrer un interpréteur de commandes Bluetooth interactif. 
   
    ```
    sudo systemctl start bluetooth
    bluetoothctl
    ```
4. Entrez la commande **power on** pour mettre le contrôleur bluetooth sous tension. La sortie doit ressembler à celle-ci :
   
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```
5. Toujours dans le shell interactif bluetooth, entrez la commande **scan on** pour rechercher des appareils bluetooth. La sortie doit ressembler à celle-ci :
   
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```
6. Appuyez sur le petit bouton de l’appareil SensorTag pour le rendre détectable (le voyant vert doit clignoter). Le Raspberry Pi 3 doit détecter l’appareil SensorTag :
   
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
   
    Dans cet exemple, vous pouvez voir que l’adresse MAC de l’appareil SensorTag est **A0:E6:F8:B5:F6:00**.
7. Désactivez l’analyse en entrant la commande **scan off** .
   
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```
8. Connectez-vous à votre appareil SensorTag à l’aide de son adresse MAC en entrant **connect \<adresse MAC>**. Notez que l’exemple de sortie ci-dessous est abrégé :
   
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
   
    > Notez que vous pouvez nouveau répertorier de nouveau les caractéristiques GATT de l’appareil à l’aide de la commande **list-attributes**.
9. Vous pouvez maintenant vous déconnecter de l’appareil à l’aide de la commande **disconnect**, puis utilisez la commande **quit** pour quitter le shell Bluetooth :
   
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Vous êtes maintenant prêt à exécuter l’exemple de passerelle BLE sur votre appareil Raspberry Pi 3.

## <a name="run-the-ble-gateway-sample"></a>Exécution de l’exemple de passerelle BLE
Pour exécuter l’exemple BLE, vous devez effectuer trois tâches :

* Configurer deux exemples d’appareils dans votre IoT Hub.
* Générer le Kit de développement logiciel (SDK) de passerelle IoT sur votre appareil Raspberry Pi 3.
* Configurer et exécuter l’exemple de BLE sur votre appareil Raspberry Pi 3.

Lors de la rédaction du présent article, le Kit de développement logiciel (SDK) de passerelle IoT prenait uniquement en charge les passerelles utilisant des modules BLE sur Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configuration de deux exemples d’appareils dans votre IoT Hub
* [Créez un IoT Hub][lnk-create-hub] dans votre abonnement Azure (pour effectuer cette procédure pas à pas, vous aurez besoin du nom de votre hub). Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* Ajoutez un appareil nommé **SensorTag_01** à votre IoT Hub et notez son ID et sa clé d’appareil. Vous pouvez vous servir des outils de l’[Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour ajouter cet appareil à l’IoT Hub que vous avez créé à l’étape précédente, et récupérer sa clé. Vous allez mapper cet appareil à l’appareil SensorTag lors de la configuration de la passerelle.

### <a name="build-the-azure-iot-gateway-sdk-on-your-raspberry-pi-3"></a>Générer le Kit de développement logiciel (SDK) de passerelle Azure IoT sur votre Raspberry Pi 3

Installez les dépendances pour le Kit de développement logiciel (SDK) de passerelle Azure IoT.

``` 
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```
Utilisez les commandes suivantes pour cloner le Kit de développement logiciel (SDK) de passerelle IoT et tous ses sous-modules dans votre répertoire de base :

```
cd ~
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
cd azure-iot-gateway-sdk
git submodule update --init --recursive
```

Si vous disposez d’une copie complète du référentiel du Kit de développement logiciel (SDK) de passerelle IoT sur votre Raspberry Pi 3, vous pouvez générer la passerelle en utilisant la commande suivante à partir du dossier contenant le Kit de développement logiciel (SDK) :

```
./tools/build.sh --skip-unittests --skip-e2e-tests
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Configurer et exécuter l’exemple de BLE sur votre appareil Raspberry Pi 3
Pour démarrer et exécuter l’exemple, vous devez configurer chaque module qui fait partie de la passerelle. Cette configuration est fournie dans un fichier JSON et vous devez configurer les cinq modules participants. Le référentiel contient un exemple de fichier JSON nommé **gateway_sample.json** que vous pouvez utiliser comme point de départ pour créer votre propre fichier de configuration. Ce fichier se trouve dans le dossier **samples/ble_gateway_hl/src**, dans la copie locale du référentiel du Kit de développement logiciel (SDK) de passerelle IoT.

Les sections suivantes décrivent comment modifier ce fichier de configuration pour l’exemple BLE, et supposent que le référentiel du Kit de développement logiciel (SDK) de passerelle IoT se trouve dans le dossier **/home/pi/azure-iot-gateway-sdk/** sur votre Raspberry Pi 3. Si le référentiel se trouve ailleurs, vous devez ajuster les chemins d’accès en conséquence :

#### <a name="logger-configuration"></a>Configuration de l’enregistreur
En supposant que le référentiel de la passerelle se trouve dans le dossier **/home/pi/azure-iot-gateway-sdk/**, configurez le module enregistreur comme suit :

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>Configuration du module BLE
L’exemple de configuration de l’appareil BLE suppose qu’il s’agit d’un appareil Texas Instruments SensorTag. Tout appareil BLE standard qui peut fonctionner comme un périphérique GATT devrait fonctionner, mais vous devrez mettre à jour les ID et les données des caractéristiques GATT (pour obtenir des instructions d’écriture). Ajoutez l’adresse MAC de votre appareil SensorTag : 

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>module IoT Hub
Ajoutez le nom de votre IoT Hub. La valeur de suffixe est généralement **azure-devices.net**:

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuration du mappage d’identité
Ajoutez l’adresse MAC de votre appareil SensorTag, ainsi que l’ID et la clé de l’appareil **SensorTag_01** que vous avez ajoutés à votre IoT Hub :

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "AA:BB:CC:DD:EE:FF",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuration du module d’imprimante BLE
```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>Configuration du module BLEC2D
```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>Configuration du routage
La configuration suivante permet de s’assurer que :

* Le module **Enregistreur** reçoit et journalise tous les messages.
* Le module **SensorTag** envoie les messages aux modules **mapping** et **BLE Printer**.
* Le module **mapping** envoie les messages à envoyer à votre IoT Hub au module **IoTHub**.
* Le module **IoTHub** renvoie les messages au module **mapping**.
* Le module **mapping** envoie les messages au module **BLEC2D**.
* Le module **BLEC2D** renvoie les messages au module **SensorTag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

Pour exécuter l’exemple, transmettez le chemin d’accès du fichier de configuration JSON au fichier binaire **ble_gateway**. Si vous avez utilisé le fichier **gateway_sample.json**, la commande figure ci-dessous. Exécutez-la à partir du répertoire azure-iot-gateway-sdk directory.

```
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Vous devrez peut-être appuyer sur le petit bouton situé sur l’appareil SensorTag pour le rendre détectable avant d’exécuter l’exemple.

Lorsque vous exécutez l’exemple, vous pouvez vous servir de l’outil de l’[Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour surveiller les messages que la passerelle transfère à partir de l’appareil SensorTag.

## <a name="send-cloud-to-device-messages"></a>Envoi de messages cloud vers appareil
Le module BLE prend également en charge l’envoi d’instructions à partir d’Azure IoT Hub à l’appareil. Vous pouvez utiliser l’[Explorateur d’appareils Azure IoT Hub](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) ou l’[Explorateur IoT Hub](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) pour envoyer des messages JSON que le module de passerelle BLE transmet à l’appareil BLE.
Si vous utilisez l’appareil SensorTag de Texas Instruments, vous pouvez activer la DEL rouge, la DEL verte, ou le vibreur sonore en envoyant des commandes à partir de l’IoT Hub. Pour ce faire, commencez par envoyer les deux messages JSON suivants dans l’ordre. Ensuite, vous pouvez envoyer n’importe laquelle des commandes pour activer les témoins ou le vibreur sonore.

1 Réinitialisez les DEL et le vibreur sonore (en les mettant hors tension)
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```
2 Configures les E/S en tant que « à distance »
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Allumer le voyant rouge
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Allumer le voyant vert
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```
* Allumer l’alarme sonore
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez approfondir vos connaissances sur le Kit de développement logiciel (SDK) de passerelle IoT et découvrir certains exemples de code, consultez les didacticiels de développement et les ressources suivants :

* [Kit de développement logiciel (SDK) de passerelle Azure IoT][lnk-sdk]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Nov16_HO5-->


