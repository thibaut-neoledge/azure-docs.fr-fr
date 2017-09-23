---
title: Utiliser un appareil physique avec Azure IoT Edge | Microsoft Docs
description: "Guide pratique d’utilisation d’un appareil SensorTag de Texas Instruments pour envoyer des données à un IoT Hub via une passerelle IoT Edge s’exécutant sur un Raspberry Pi 3. La passerelle est créée à l’aide d’Azure IoT Edge."
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
ms.date: 06/12/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 02962a91c739a53dfcf947bcc736e5c293b9384f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/26/2017

---
# <a name="use-azure-iot-edge-on-a-raspberry-pi-to-forward-device-to-cloud-messages-to-iot-hub"></a>Utiliser Azure IoT Edge sur un Raspberry Pi pour transférer les messages Appareil vers cloud à IoT Hub

Cette procédure pas à pas de [l’exemple à faible consommation d’énergie Bluetooth][lnk-ble-samplecode] vous montre comment utiliser [la passerelle Azure IoT Edge][lnk-sdk] pour :

* Transférer la télémétrie appareil-vers-cloud vers IoT Hub à partir d’un appareil physique.
* Achemine les commandes vers un appareil physique à partir d’IoT Hub.

Cette procédure pas à pas inclut les étapes suivantes :

* **Architecture**: informations architecturales importantes concernant l'exemple à faible consommation d’énergie Bluetooth.
* **Créer et exécuter**: les étapes requises pour créer et exécuter l’exemple.

## <a name="architecture"></a>Architecture

La procédure pas à pas vous montre comment générer et exécuter une passerelle IoT Edge sur un Raspberry Pi 3 exécutant Raspbian Linux. La passerelle est créée à l’aide d’IoT Edge. L’exemple utilise un appareil Texas Instruments SensorTag Bluetooth Low Energy (BLE) pour collecter des données de température.

Lorsque vous exécutez la passerelle IoT Edge, celle-ci :

* Se connecte à un appareil SensorTag à l’aide du protocole Bluetooth Low Energy (BLE).
* Se connecte à IoT Hub à l’aide du protocole HTTP.
* Transfère les données de télémétrie à partir de l’appareil SensorTag vers IoT Hub.
* Achemine les commandes vers l’appareil SensorTag à partir d’IoT Hub.

La passerelle contient les modules IoT Edge suivants :

* Un *module BLE* qui interagit avec un appareil BLE pour recevoir les données de température à partir de l’appareil et envoyer des commandes à l’appareil.
* Un *module cloud-à-appareil BLE* qui traduit les messages JSON provenant d’IoT Hub en instructions BLE pour le *module BLE*.
* Un *module enregistreur* qui journalise tous les messages de la passerelle dans un fichier local.
* Un *module de mappage d’identité* qui effectue la traduction entre les adresses MAC de l’appareil BLE et les identités des appareils Azure IoT Hub.
* Un *module IoT Hub* qui transfère les données de télémétrie sur un hub IoT et reçoit les commandes de l’appareil à partir d’un hub IoT.
* Un *module d’imprimante BLE* qui interprète les données de télémétrie fournies par l’appareil BLE et imprime des données mises en forme sur la console pour permettre la résolution des problèmes et le débogage.

### <a name="how-data-flows-through-the-gateway"></a>Flux des données sur la passerelle

Le diagramme de blocs suivant illustre le pipeline du flux de téléchargement des données de télémétrie :

![Pipeline de passerelle de téléchargement de télémétrie](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

Les étapes qu’un élément de télémétrie suit lors de son transfert entre un appareil BLE et IoT Hub sont les suivantes :

1. L’appareil BLE génère un exemple de température et l’envoie au module BLE de la passerelle via Bluetooth.
1. Le module BLE reçoit l’exemple et le publie dans le répartiteur avec l’adresse MAC de l’appareil.
1. Le module de mappage d’identité récupère ce message et utilise une table interne pour convertir l’adresse MAC de l’appareil en une identité d’appareil IoT Hub. Une identité d’appareil IoT Hub se compose d’un ID d’appareil et d’une clé d’appareil.
1. Le module de mappage d’identité publie un nouveau message contenant les exemples de données de température, l’adresse MAC de l’appareil, l’ID de l’appareil et la clé de l’appareil.
1. Le module IoT Hub reçoit ce nouveau message (généré par le module de mappage d’identité) et le publie dans IoT Hub.
1. Le module enregistreur journalise tous les messages reçus du répartiteur dans un fichier local.

Le diagramme de blocs suivant illustre le pipeline du flux des données de commandes de l’appareil :

![Pipeline de passerelle de commande d’appareil](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. Le module IoT Hub interroge régulièrement IoT Hub pour savoir s’il existe de nouveaux messages de commande.
1. Lorsque le module IoT Hub reçoit un nouveau message de commande, il le publie sur le répartiteur.
1. Le module de mappage d’identité récupère le message de commande et utilise une table interne pour convertir l’ID d’appareil IoT Hub en adresse MAC d’appareil. Il publie ensuite un nouveau message contenant l’adresse MAC de l’appareil cible dans le mappage des propriétés du message.
1. Le module cloud-à-appareil BLE récupère ce message et le traduit dans l’instruction BLE appropriée pour le module BLE. Il publie ensuite un nouveau message.
1. Le module BLE récupère ce message et exécute l’instruction d’E/S en communiquant avec l’appareil BLE.
1. Le module enregistreur consigne tous les messages reçus du répartiteur dans un fichier sur le disque.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure actif.

> [!NOTE]
> Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite d’Azure][lnk-free-trial].

Vous devez installer le client SSH sur votre ordinateur de bureau afin de pouvoir accéder à distance à la ligne de commande sur le Raspberry Pi.

- Windows n’inclut pas de client SSH. Nous vous recommandons d’utiliser [PuTTY](http://www.putty.org/).
- La plupart des distributions Linux et Mac OS incluent l’utilitaire de ligne de commande SSH. Pour plus d’informations, consultez [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (Utilisation de SSH avec Linux ou Mac OS).

## <a name="prepare-your-hardware"></a>Préparation du matériel

Ce didacticiel suppose que vous utilisez un appareil [SensorTag de Texas Instruments](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) connecté à un Raspberry Pi 3 exécutant Raspbian.

### <a name="install-raspbian"></a>Installer Raspbian

Vous pouvez utiliser l’une des options suivantes pour installer Raspbian sur votre appareil Raspberry Pi 3.

* Utilisez l’interface utilisateur graphique [NOOBS][lnk-noobs] pour installer la dernière version de Raspbian.
* [Téléchargez][lnk-raspbian] Manuellement et écrivez la dernière image du système d’exploitation Raspbian sur une carte SD.

### <a name="sign-in-and-access-the-terminal"></a>Se connecter et accéder au terminal

Deux méthodes vous permettent d’accéder à un environnement de type terminal sur votre Raspberry Pi :

* Si vous disposez d’un clavier et d’un moniteur connectés à votre Raspberry Pi, vous pouvez utiliser l’interface utilisateur graphique Raspbian pour accéder à une fenêtre de terminal.

* Accédez à la ligne de commande sur votre Raspberry Pi à l’aide de SSH à partir de votre ordinateur de bureau.

#### <a name="use-a-terminal-window-in-the-gui"></a>Utiliser une fenêtre de terminal dans l’interface utilisateur graphique

Les informations d’identification par défaut pour Raspbian sont le nom d’utilisateur **pi** et le mot de passe **raspberry**. Dans la barre des tâches dans l’interface utilisateur graphique, vous pouvez lancer l’utilitaire **Terminal** à l’aide de l’icône qui ressemble à un moniteur.

#### <a name="sign-in-with-ssh"></a>Se connecter avec SSH

Vous pouvez utiliser SSH pour accéder à l’aide de la ligne de commande à votre Raspberry Pi. L’article [SSH (Secure Shell)][lnk-pi-ssh] décrit la configuration de SSH sur votre Raspberry Pi et la connexion à partir des systèmes d’exploitation [Windows][lnk-ssh-windows] ou [Linux et Mac][lnk-ssh-linux].

Connectez-vous avec le nom d’utilisateur **pi** et le mot de passe **raspberry**.

### <a name="install-bluez-537"></a>Installer BlueZ 5.37

Les modules BLE communiquent avec le matériel Bluetooth via la pile BlueZ. Pour que les modules fonctionnent correctement, vous avez besoin de la version 5.37 de BlueZ. En suivant ces instructions, vous êtes assuré que la version installée de BlueZ est correcte.

1. Arrêtez le démon Bluetooth actuel :

    ```sh
    sudo systemctl stop bluetooth
    ```

1. Installez les dépendances BlueZ :

    ```sh
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```

1. Téléchargez le code source de BlueZ à partir de bluez.org :

    ```sh
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```

1. Décompressez le code source :

    ```sh
    tar -xvf bluez-5.37.tar.xz
    ```

1. Déplacer les répertoires vers le dossier nouvellement créé :

    ```sh
    cd bluez-5.37
    ```

1. Configurez le code BlueZ à générer :

    ```sh
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```

1. Générez BlueZ :

    ```sh
    make
    ```

1. Installez BlueZ une fois la génération terminée :

    ```sh
    sudo make install
    ```

1. Modifiez la configuration du service systemd pour Bluetooth afin qu’il pointe vers le nouveau démon Bluetooth dans le fichier `/lib/systemd/system/bluetooth.service`. Remplacez la ligne « ExecStart » par le texte suivant :

    ```conf
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Activer la connectivité à l’appareil SensorTag à partir de votre appareil Raspberry Pi 3

Avant d’exécuter l’exemple, vous devez vérifier que votre Raspberry Pi 3 peut se connecter à l’appareil SensorTag.

1. Vérifiez que l’utilitaire `rfkill` est installé :

    ```sh
    sudo apt-get install rfkill
    ```

1. Débloquez Bluetooth sur l’appareil Raspberry Pi 3, et vérifiez que le numéro de version est **5.37** :

    ```sh
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```

1. Pour entrer l’interpréteur de commandes Bluetooth interactif, démarrez le service Bluetooth et exécutez la commande **bluetoothctl** :

    ```sh
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Entrez la commande **power on** pour mettre le contrôleur bluetooth sous tension. La commande retourne un résultat semblable à l’exemple suivant :

    ```sh
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. Dans le shell interactif bluetooth, entrez la commande **scan on** pour rechercher des appareils bluetooth. La commande retourne un résultat semblable à l’exemple suivant :

    ```sh
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. Appuyez sur le petit bouton de l’appareil SensorTag pour le rendre détectable (le voyant vert doit clignoter). Le Raspberry Pi 3 doit détecter l’appareil SensorTag :

    ```sh
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    Dans cet exemple, vous pouvez voir que l’adresse MAC de l’appareil SensorTag est **A0:E6:F8:B5:F6:00**.

1. Désactivez l’analyse en entrant la commande **scan off** :

    ```sh
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. Connectez-vous à votre appareil SensorTag à l’aide de son adresse MAC en entrant **connect \<adresse MAC>\>**. L’exemple de sortie suivant est abrégé pour plus de clarté :

    ```sh
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

    > Vous pouvez de nouveau répertorier les caractéristiques GATT de l’appareil à l’aide de la commande **list-attributes** .

1. Vous pouvez maintenant vous déconnecter de l’appareil à l’aide de la commande **disconnect**, puis utilisez la commande **quit** pour quitter le shell Bluetooth :

    ```sh
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Vous êtes maintenant prêt à exécuter l’exemple de passerelle IoT Edge BLE sur votre appareil Raspberry Pi 3.

## <a name="run-the-iot-edge-ble-sample"></a>Exécuter l’exemple BLE IoT Edge

Pour exécuter l’exemple BLE IoT Edge, vous devez effectuer trois tâches :

* Configurer deux exemples d’appareils dans votre IoT Hub.
* Générer IoT Edge sur votre appareil Raspberry Pi 3.
* Configurer et exécuter l’exemple de BLE sur votre appareil Raspberry Pi 3.

Lors de la rédaction du présent article, IoT Edge prenait uniquement en charge les modules BLE sur des passerelles sur Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configuration de deux exemples d’appareils dans votre IoT Hub

* [Créer un IoT Hub][lnk-create-hub] dans votre abonnement Azure (vous aurez besoin du nom de votre concentrateur pour effectuer cette procédure pas à pas). Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* Ajoutez un appareil nommé **SensorTag_01** à votre IoT Hub et notez son ID et sa clé d’appareil. Vous pouvez vous servir des outils de [l’Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour ajouter cet appareil à l’IoT Hub que vous avez créé à l’étape précédente, et récupérer sa clé. Vous allez mapper cet appareil à l’appareil SensorTag lors de la configuration de la passerelle.

### <a name="build-azure-iot-edge-on-your-raspberry-pi-3"></a>Générer Azure IoT Edge sur votre appareil Raspberry Pi 3

Installer les dépendances pour Azure IoT Edge :

```sh
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```

Utilisez les commandes suivantes pour cloner IoT Edge et tous ses sous-modules dans votre répertoire de base :

```sh
cd ~
git clone https://github.com/Azure/iot-edge.git
```

Si vous disposez d’une copie complète du dépôt IoT Edge sur votre Raspberry Pi 3, vous pouvez générer la passerelle en utilisant la commande suivante à partir du dossier contenant le Kit de développement logiciel (SDK) :

```sh
cd ~/iot-edge
./tools/build.sh  --disable-native-remote-modules
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Configurer et exécuter l’exemple de BLE sur votre appareil Raspberry Pi 3

Pour démarrer et exécuter l’exemple, vous devez configurer chaque module IoT Edge qui fait partie de la passerelle. Cette configuration est fournie dans un fichier JSON et vous devez configurer les cinq modules IoT Edge participants. Le référentiel contient un exemple de fichier JSON nommé **gateway\_sample.json** que vous pouvez utiliser comme point de départ pour créer votre propre fichier de configuration. Ce fichier se trouve dans le dossier **samples/ble_gateway_hl/src**, dans la copie locale du dépôt IoT Edge.

Les sections suivantes décrivent comment modifier ce fichier de configuration pour l’exemple BLE et supposent que le dépôt IoT Edge se trouve dans le dossier **/home/pi/iot-edge/** sur votre Raspberry Pi 3. Si le référentiel se trouve ailleurs, vous devez ajuster les chemins d’accès en conséquence.

#### <a name="logger-configuration"></a>Configuration de l’enregistreur

En supposant que le dépôt de la passerelle se trouve dans le dossier **/home/pi/iot-edge/**, configurez le module enregistreur comme suit :

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

L’exemple de configuration de l’appareil BLE suppose qu’il s’agit d’un appareil Texas Instruments SensorTag. Tout appareil BLE standard qui peut fonctionner comme un périphérique GATT devrait fonctionner, mais vous devrez peut-être mettre à jour les ID et les données des caractéristiques GATT. Ajoutez l’adresse MAC de votre appareil SensorTag :

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

Si vous n’utilisez pas d’appareil SensorTag, consultez la documentation de votre appareil BLE afin de déterminer si vous devez mettre à jour les ID et les données des caractéristiques GATT.

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
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
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

La configuration suivante permet d’assurer l’acheminement suivant entre les modules IoT Edge :

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

Pour exécuter l’exemple, transmettez le chemin d’accès du fichier de configuration JSON en tant que paramètre au fichier binaire **ble\_gateway**. La commande suivante considère que vous utilisez le fichier de configuration **gateway_sample.json**. Exécutez-la à partir du dossier **iot-edge** sur le Raspberry Pi :

```sh
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Vous devrez peut-être appuyer sur le petit bouton situé sur l’appareil SensorTag pour le rendre détectable avant d’exécuter l’exemple.

Lorsque vous exécutez l’exemple, vous pouvez vous servir de [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ou de l’outil [iothub-explorer](https://github.com/Azure/iothub-explorer) pour surveiller les messages que la passerelle IoT Edge transfère à partir de l’appareil SensorTag. Par exemple, à l’aide d’iothub-explorer, vous pouvez surveiller les messages appareil-à-cloud à l’aide de la commande suivante :

```sh
iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
```

## <a name="send-cloud-to-device-messages"></a>Envoi de messages cloud vers appareil

Le module BLE prend également en charge l’envoi de commandes à partir d’IoT Hub à l’appareil. Vous pouvez utiliser [l’Explorateur d’appareils](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ou l’outil [iothub-explorer](https://github.com/Azure/iothub-explorer) pour envoyer des messages JSON que le module de passerelle BLE transmet à l’appareil BLE.

Si vous utilisez l’appareil SensorTag de Texas Instruments, vous pouvez activer la DEL rouge, la DEL verte, ou le vibreur sonore en envoyant des commandes à partir de l’IoT Hub. Avant d’envoyer des commandes depuis IoT Hub, commencez par envoyer les deux messages JSON suivants dans l’ordre. Ensuite, vous pouvez envoyer n’importe laquelle des commandes pour activer les témoins ou le vibreur sonore.

1. Réinitialiser (éteindre) les LED et le vibreur sonore (en les mettant hors tension) :

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. Configurer les E/S en tant que « à distance » :

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

Maintenant, vous pouvez envoyer l’une des commandes suivantes pour allumer les voyants ou le vibreur sonore sur l’appareil SensorTag :

* Allumer la LED rouge :

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* Allumer la LED verte :

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* Allumer le vibreur sonore :

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez approfondir vos connaissances sur Azure IoT Edge et découvrir certains exemples de code, consultez les tutoriels de développement et les ressources suivants :

* [Azure IoT Edge][lnk-sdk]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md

