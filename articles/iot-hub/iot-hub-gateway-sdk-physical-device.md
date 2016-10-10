<properties
	pageTitle="Utiliser un appareil réel avec le Kit de développement logiciel (SDK) de passerelle | Microsoft Azure"
	description="Procédure pas à pas pour le Kit de développement logiciel (SDK) de passerelle Azure IoT Hub utilisant un appareil Texas Instruments SensorTag pour envoyer des données à IoT Hub par le biais d’une passerelle exécutée sur un module Intel Edison Compute"
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# Kit de développement logiciel (SDK) de passerelle IoT (version bêta) : envoyer des messages appareil vers cloud avec un appareil réel à l’aide de Linux

Cette procédure pas à pas sur l’[exemple à faible consommation d’énergie Bluetooth][lnk-ble-samplecode] vous montre comment utiliser le [Kit de développement logiciel (SDK) de passerelle IoT Microsoft Azure][lnk-sdk] pour transférer des données de télémétrie appareil vers cloud vers IoT Hub à partir d’un appareil physique et comment acheminer des commandes à partir d’IoT Hub vers un appareil physique.

Cette procédure pas à pas inclut les étapes suivantes :

* **Architecture** : informations architecturales importantes concernant l'exemple à faible consommation d’énergie Bluetooth.

* **Créer et exécuter** : les étapes requises pour créer et exécuter l’exemple.

## Architecture

La procédure pas à pas vous montre comment créer et exécuter une passerelle IoT sur un module de calcul Intel Edison exécutant Linux. La passerelle est construite à l’aide du Kit de développement logiciel (SDK) de passerelle IoT. L’exemple utilise un appareil Texas Instruments SensorTag Bluetooth Low Energy (BLE) pour collecter des données de température.

Lorsque vous exécutez la passerelle, celle-ci :

- Se connecte à un appareil SensorTag à l’aide du protocole Bluetooth Low Energy (BLE).
- Se connecte à IoT Hub à l’aide du protocole AMQP.
- Transfère les données de télémétrie à partir de l’appareil SensorTag vers IoT Hub.
- Achemine les commandes vers l’appareil SensorTag à partir d’IoT Hub.

La passerelle contient les modules suivants :

- Un *module BLE* qui interagit avec un appareil BLE pour recevoir les données de température à partir de l’appareil et envoyer des commandes à l’appareil.
- Un *module cloud-à-appareil BLE* qui traduit les messages JSON provenant du cloud en instructions BLE pour le *module BLE*.
- Un *module enregistreur* qui consigne tous les messages de passerelle.
- Un *module de mappage d’identité* qui effectue la traduction entre les adresses MAC de l’appareil BLE et les identités des appareils Azure IoT Hub.
- Un *module IoT Hub* qui transfère les données de télémétrie sur un hub IoT et reçoit les commandes de l’appareil à partir d’un hub IoT.
- Un *module d’imprimante BLE* qui interprète les données de télémétrie fournies par l’appareil BLE et imprime des données mises en forme sur la console pour permettre la résolution des problèmes et le débogage.

### Flux des données sur la passerelle

Le diagramme de blocs suivant illustre le pipeline du flux de téléchargement des données de télémétrie :

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Les étapes qu’un élément de télémétrie suit lors de son transfert entre un appareil BLE et IoT Hub sont les suivantes :

1. L’appareil BLE génère un exemple de température et l’envoie au module BLE de la passerelle via Bluetooth.
2. Le module BLE reçoit l’exemple et le publie dans le répartiteur avec l’adresse MAC de l’appareil.
3. Le module de mappage d’identité récupère ce message et utilise une table interne pour convertir l’adresse MAC de l’appareil en une identité d’appareil IoT Hub (un ID d’appareil et une clé d’appareil). Il publie ensuite un nouveau message contenant les exemples de données de température, l’adresse MAC de l’appareil, l’ID de l’appareil et la clé de l’appareil.
4. Le module IoT Hub reçoit ce nouveau message (généré par le module de mappage d’identité) et le publie dans IoT Hub.
5. Le module enregistreur consigne tous les messages reçus du répartiteur dans un fichier sur le disque.

Le diagramme de blocs suivant illustre le pipeline du flux des données de commandes de l’appareil :

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Le module IoT Hub interroge régulièrement IoT Hub pour savoir s’il existe de nouveaux messages de commande.
2. Lorsque le module IoT Hub reçoit un nouveau message de commande, il le publie sur le répartiteur.
3. Le module de mappage d’identité récupère le message de commande et utilise une table interne pour convertir l’ID d’appareil IoT Hub en adresse MAC d’appareil. Il publie ensuite un nouveau message contenant l’adresse MAC de l’appareil cible dans le mappage des propriétés du message.
4. Le module cloud-à-appareil BLE récupère ce message et le traduit en instruction BLE correcte pour le module BLE. Il publie ensuite un nouveau message.
5. Le module BLE récupère ce message et exécute l’instruction d’E/S en communiquant avec l’appareil BLE.
6. Le module enregistreur consigne tous les messages reçus du répartiteur dans un fichier sur le disque.

## Préparation du matériel

Ce didacticiel suppose que vous utilisez un appareil [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) connecté à une carte Intel Edison.

### Configuration de la carte Edison

Avant de commencer, vous devez vous assurer que vous pouvez connecter votre appareil Edison à votre réseau sans fil. Pour configurer votre appareil Edison, vous devez le connecter à un ordinateur hôte. Intel fournit des guides de démarrage pour les systèmes d’exploitation suivants :

- [Get Started with the Intel Edison Development Board on Windows 64-bit][lnk-setup-win64] (Guide de démarrage de la carte Intel Edison Development sur Windows 64 bits).
- [Get Started with the Intel Edison Development Board on Windows 32-bit][lnk-setup-win32] (Guide de démarrage de la carte Intel Edison Development sur Windows 32 bits).
- [Get Started with the Intel Edison Development Board on Mac OS X][lnk-setup-osx] (Guide de démarrage de la carte Intel Edison Development sur Mac OS X).
- [Getting Started with the Intel® Edison Board on Linux][lnk-setup-linux] (Guide de démarrage de la carte Intel® Edison sur Linux).

Pour configurer votre appareil Edison et vous familiariser avec celui-ci, vous devez effectuer toutes les étapes de ces articles « de démarrage », à l’exception de la dernière étape, « Choose IDE » (Sélection d’IDE), qui ne s’applique pas à ce didacticiel. À la fin du processus de configuration Edison, vous avez :

- Mis à jour votre appareil Edison vers le dernier microprogramme disponible.
- Créé une connexion en série à partir de l’hôte vers l’appareil Edison.
- Exécutez le script **configure\_edison** pour définir un mot de passe et activer la connexion Wi-Fi sur votre appareil Edison.

### Activation de la connectivité à l’appareil SensorTag à partir de votre carte Edison

Avant d’exécuter l’exemple, vous devez vérifier que votre carte Edison peut se connecter à l’appareil SensorTag.

Vous devez d’abord vérifier que votre carte Edison peut se connecter à l’appareil SensorTag.

1. Débloquez Bluetooth sur l’appareil Edison et vérifiez que le numéro de version est **5.37**.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. Exécutez la commande **bluetoothctl**. Vous êtes maintenant dans un shell Bluetooth interactif.

3. Entrez la commande **power on** pour mettre le contrôleur bluetooth sous tension. La sortie doit ressembler à celle-ci :
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

4. Toujours dans le shell interactif bluetooth, entrez la commande **scan on** pour rechercher des appareils bluetooth. La sortie doit ressembler à celle-ci :
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

5. Appuyez sur le petit bouton de l’appareil SensorTag pour le rendre détectable (le voyant vert doit clignoter). L’appareil Edison doit détecter l’appareil SensorTag :
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    Dans cet exemple, vous pouvez voir que l’adresse MAC de l’appareil SensorTag est **A0:E6:F8:B5:F6:00**.

6. Désactivez l’analyse en entrant la commande **scan off**.
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

7. Connectez-vous à votre appareil SensorTag à l’aide de son adresse MAC en entrant **connect <adresse MAC>**. Notez que l’exemple de sortie ci-dessous est abrégé :
    
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
    
    Remarque : vous pouvez de nouveau répertorier les caractéristiques GATT de l’appareil à l’aide de la commande **list-attributes**.

8. Vous pouvez maintenant vous déconnecter de l’appareil à l’aide de la commande **disconnect**, puis utilisez la commande **quit** pour quitter le shell Bluetooth :
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Vous êtes maintenant prêt à exécuter l’exemple de passerelle BLE sur votre appareil Edison.

## Exécution de l’exemple de passerelle BLE

Pour exécuter l’exemple BLE sur votre appareil Edison, vous devez effectuer trois tâches :

- Configurer deux exemples d’appareils dans votre IoT Hub.
- Générer le Kit de développement logiciel (SDK) de passerelle sur votre appareil Edison.
- Configurer et exécuter l’exemple BLE sur votre appareil Edison.

Lors de la rédaction du présent article, le Kit de développement logiciel (SDK) de passerelle prenait uniquement en charge les passerelles utilisant des modules BLE sur Linux.

### Configuration de deux exemples d’appareils dans votre IoT Hub

- [Créez un concentrateur IoT][lnk-create-hub] dans votre abonnement Azure (vous aurez besoin du nom de votre concentrateur pour effectuer cette procédure pas à pas). Si vous ne possédez pas déjà d’abonnement Azure, vous pouvez obtenir un [compte gratuit][lnk-free-trial].
- Ajoutez un appareil nommé **SensorTag\_01** à votre concentrateur IoT et notez son ID et sa clé d’appareil. Vous pouvez utiliser les outils [Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour ajouter cet appareil au concentrateur IoT que vous avez créé à l’étape précédente et récupérer ainsi sa clé. Vous allez mapper cet appareil à l’appareil SensorTag lors de la configuration de la passerelle.

### Génération du Kit de développement logiciel (SDK) de passerelle sur votre appareil Edison

La version de **git** sur l’appareil Edison ne prend pas en charge les modules secondaires. Vous disposez de deux options pour télécharger la source complète du Kit de développement logiciel (SDK) de passerelle sur l’appareil Edison :

- Option 1 : Clonez le référentiel [Kit de développement logiciel (SDK) de passerelle IoT Microsoft Azure][lnk-sdk] sur votre appareil Edison, puis clonez manuellement le référentiel pour chaque module secondaire.
- Option 2 : Clonez le référentiel [Kit de développement logiciel (SDK) de passerelle IoT Microsoft Azure][lnk-sdk] sur un ordinateur de bureau où **git** prend en charge les modules secondaires, puis copiez le référentiel complet avec les modules secondaires sur votre appareil Edison.

Si vous choisissez l’option 2, utilisez les commandes **git** suivantes pour cloner le Kit de développement logiciel (SDK) de passerelle et tous ses modules secondaires :

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

Vous devez ensuite compresser l’intégralité du référentiel local dans un fichier d’archive unique avant de le copier sur l’appareil Edison. Vous pouvez utiliser un utilitaire tel que **pscp** qui est fourni avec **Putty** pour copier le fichier d’archive sur l’appareil Edison. Par exemple :

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Lorsque vous avez une copie complète du référentiel du Kit de développement logiciel (SDK) de passerelle sur votre appareil Edison, vous pouvez le générer à l’aide de la commande suivante à partir du dossier qui contient le Kit de développement logiciel (SDK) :

```
./tools/build.sh
```

### Configuration et exécution de l’exemple BLE sur votre appareil Edison

Pour démarrer et exécuter l’exemple, vous devez configurer chaque module qui fait partie de la passerelle. Cette configuration est fournie dans un fichier JSON et vous devez configurer les cinq modules participants. Le référentiel contient un exemple de fichier JSON nommé **gateway\_sample.json** que vous pouvez utiliser comme point de départ pour créer votre propre fichier de configuration. Ce fichier se trouve dans le dossier **samples/ble\_gateway\_hl/src** dans la copie locale du référentiel du Kit de développement logiciel (SDK) de passerelle.

Les sections suivantes décrivent comment modifier ce fichier de configuration pour l’exemple BLE et supposent que le référentiel du Kit de développement logiciel (SDK) de passerelle se trouve dans le dossier **/home/root/azure-iot-gateway-sdk/** sur votre appareil Edison. Si le référentiel se trouve ailleurs, vous devez ajuster les chemins d’accès en conséquence :

#### Configuration de l’enregistreur

En supposant que le référentiel de la passerelle se trouve dans le dossier **/home/root/azure-iot-gateway-sdk/**, configurez le module enregistreur comme suit :

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### Configuration du module BLE

L’exemple de configuration de l’appareil BLE suppose qu’il s’agit d’un appareil Texas Instruments SensorTag. Tout appareil BLE standard qui peut fonctionner comme un périphérique GATT devrait fonctionner, mais vous devrez mettre à jour les ID et les données des caractéristiques GATT (pour obtenir des instructions d’écriture). Ajoutez l’adresse MAC de votre appareil SensorTag :

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
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

#### Module IoT Hub

Ajoutez le nom de votre IoT Hub. La valeur de suffixe est généralement **azure-devices.net** :

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### Configuration du mappage d’identité

Ajoutez l’adresse MAC de votre appareil SensorTag, ainsi que l’ID et la clé de l’appareil **SensorTag\_01** que vous avez ajoutés à votre IoT Hub :

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### Configuration du module d’imprimante BLE

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### Configuration de routage

La configuration suivante permet de s’assurer que :
- Le module **Logger** reçoit et consigne tous les messages.
- Le module **SensorTag** envoie les messages aux modules **mapping** et **BLE Printer**.
- Le module **mapping** envoie les messages à envoyer à votre hub IoT au module **IoTHub**.
- Le module **IoTHub** renvoie les messages au module **mapping**.
- Le module de **mapping** renvoie les messages au module **SensorTag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Pour exécuter l’exemple, vous exécutez le fichier binaire **ble\_gateway\_hl** en transférant le chemin d’accès au fichier de configuration JSON. Si vous avez utilisé le fichier **gateway\_sample.json**, la commande à exécuter ressemble à cela :

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Vous devrez peut-être appuyer sur le petit bouton situé sur l’appareil SensorTag pour le rendre détectable avant d’exécuter l’exemple.

Lorsque vous exécutez l’exemple, vous pouvez exécuter l’outil [Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour surveiller les messages que la passerelle transmet à partir de l’appareil SensorTag.

## Envoi de messages cloud vers appareil

Le module BLE prend également en charge l’envoi d’instructions à partir d’IoT Hub Azure vers l’appareil. Vous pouvez utiliser l’[Explorateur d’appareils Azure IoT Hub](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) ou l[’Explorateur IoT Hub](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) pour envoyer des messages JSON que le module de passerelle BLE transmet à l’appareil BLE. Par exemple, si vous utilisez l’appareil Texas Instruments SensorTag, vous pouvez envoyer les messages JSON suivants à l’appareil à partir d’IoT Hub.

- Réinitialiser tous les voyants et l’alarme sonore (les mettre hors tension)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- Configurer les E/S en tant que « distantes »

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Allumer le voyant rouge

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Allumer le voyant vert

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- Allumer l’alarme sonore

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

Le comportement par défaut d’un appareil utilisant le protocole HTTP pour se connecter à IoT Hub consiste à vérifier toutes les 25 minutes s’il existe une nouvelle commande. Par conséquent, si vous envoyez plusieurs commandes séparées, vous devez patienter 25 minutes pour que l’appareil reçoive chaque commande.

> [AZURE.NOTE] La passerelle vérifie également l’existence de nouvelles commandes à chaque fois qu’elle démarre. Vous pouvez donc la forcer à traiter une commande en l’arrêtant et en la redémarrant.

## Étapes suivantes

Si vous souhaitez approfondir vos connaissances sur les kits de développement logiciel (SDK) Gateway et découvrir certains exemples de code, consultez les didacticiels de développement et les ressources suivants :

- [Gérer un appareil de passerelle][lnk-manage-devices]
- [Kit de développement logiciel (SDK) de la passerelle Azure IoT][lnk-gateway-sdk]

Pour explorer davantage les capacités de IoT Hub, consultez :

- [Conception de votre solution][lnk-design]
- [Guide du développeur][lnk-devguide]
- [Exploration de la gestion des appareils à l’aide de l’exemple d’interface utilisateur][lnk-dmui]
- [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-manage-devices]: iot-hub-gateway-sdk-device-management.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0928_2016-->