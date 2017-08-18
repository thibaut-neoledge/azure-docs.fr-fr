---
title: "Connecter une passerelle à Azure IoT Suite à l’aide d’un Intel NUC | Documents Microsoft"
description: "Utilisez le Kit de passerelle commerciale Microsoft IoT et la solution préconfigurée de surveillance à distance. Utilisez la passerelle Azure IoT Edge afin d’activer un dispositif SensorTag pour vous connecter à la solution de surveillance à distance, envoyer la télémétrie vers le cloud et répondre aux méthodes appelées à partir du tableau de bord de la solution."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: e97f22340f869ef31d7b4c7b9857bf0d1d5400c5
ms.contentlocale: fr-fr
ms.lasthandoff: 06/08/2017

---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-telemetry-from-a-sensortag"></a>Connecter votre passerelle Azure IoT Edge à la solution préconfigurée de surveillance à distance et envoyer la télémétrie à partir d’un SensorTag

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

Ce didacticiel explique comment utiliser Azure IoT Edge pour envoyer les données de température et d’humidité d’un dispositif SensorTag à la solution préconfigurée de surveillance à distance. Le SensorTag se connecte à la passerelle Intel NUC en utilisant Bluetooth. Le tutoriel utilise :

- Azure IoT Edge pour implémenter un exemple de passerelle.
- la solution préconfigurée de surveillance à distance Azure IoT Suite comme serveur principal basé sur le cloud.

## <a name="overview"></a>Vue d’ensemble

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

- Déployer une instance de la solution préconfigurée de surveillance à distance dans votre abonnement Azure. Cette étape déploie et configure automatiquement plusieurs services Azure.
- Configurez votre périphérique de passerelle Intel NUC pour qu’il communique avec votre ordinateur et la solution de surveillance à distance.
- Configurez votre passerelle Intel NUC pour recevoir la télémétrie d’un dispositif SensorTag et envoyer celle-ci au tableau de bord de surveillance à distance.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[Texas Instruments BLE SensorTag][lnk-sensortag]. Ce didacticiel montre comment récupérer les données de télémétrie d’un dispositif SensorTag via Bluetooth.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> La solution de surveillance à distance configure un ensemble de services Azure dans votre abonnement Azure. Le déploiement reflète une architecture d’entreprise réelle. Pour éviter des frais de consommation Azure inutiles, supprimez votre instance de la solution préconfigurée dans azureiotsuite.com quand vous ne l’utilisez plus. Si vous avez à nouveau besoin de la solution préconfigurée, vous pouvez la recréer facilement. Pour plus d’informations sur la manière de réduire votre consommation pendant l’exécution de la solution de surveillance à distance, consultez [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuration des solutions préconfigurées Azure IoT Suite à des fins de démonstration).

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

## <a name="configure-bluetooth-connectivity"></a>Configurer la connectivité Bluetooth

Configurez Bluetooth sur l’Intel NUC pour permettre au dispositif SensorTag de se connecter et d’envoyer la télémétrie.

### <a name="find-the-mac-address-of-the-sensortag"></a>Rechercher l’adresse MAC du SensorTag

1. Dans le shell sur l’Intel NUC, exécutez la commande suivante pour débloquer le service Bluetooth :

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. Exécutez les commandes suivantes pour démarrer le service Bluetooth sur l’Intel NUC, puis entrez dans le shell Bluetooth :

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Exécutez la commande suivante pour activer le contrôleur Bluetooth :

    ```bash
    power on
    ```

    Lorsque le contrôleur est activé, vous voyez un message indiquant que l’**activation a réussi**.

1. Exécutez la commande suivante pour rechercher des périphériques Bluetooth à proximité :

    ```bash
    scan on
    ```

1. Appuyez sur le bouton d’alimentation du SensorTag pour le rendre détectable. La DEL verte clignote.

1. Lorsque vous voyez un message dans le shell indiquant que le contrôleur a détecté le SensorTag, prenez note de l’adresse MAC du périphérique. L’adresse MAC ressemble à **A0:E6:F8:B5:F6:00**. Vous aurez besoin de l’adresse MAC ultérieurement dans ce didacticiel pour configurer la passerelle.

1. Exécutez la commande suivante pour désactiver l’analyse Bluetooth :

    ```bash
    scan off
    ```

1. Exécutez la commande suivante pour vérifier que vous pouvez vous connecter au dispositif SensorTag :

    ```bash
    connect <SensorTag MAC address>
    ```

    Si la connexion aboutit, la shell affiche le message **Connexion réussie** et imprime des informations concernant le dispositif SensorTag. Si vous ne pouvez pas vous connecter, vérifiez que le SensorTag est toujours sous tension.

1. Vous pouvez vous déconnecter du SensorTag et quitter la shell Bluetooth en exécutant les commandes suivantes :

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Générer le module IoT Edge personnalisé

Vous pouvez maintenant générer le module IoT personnalisé qui permet à la passerelle d’envoyer des messages à la solution de surveillance à distance. Pour plus d’informations sur la configuration d’une passerelle et des modules IoT Edge, voir [Concepts relatifs à Azure IoT Edge][lnk-gateway-concepts].

Téléchargez le code source pour les modules IoT Edge personnalisés à partir de GitHub à l’aide des commandes suivantes :

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Générez le module IoT Edge personnalisé à l’aide des commandes suivantes :

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

Le script de génération place le module IoT Edge personnalisé libsensor2remotemonitoring.so dans le dossier de génération.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurer et exécuter la passerelle IoT Edge

Vous pouvez à présent configurer la passerelle IoT Edge pour envoyer la télémétrie de votre dispositif SensorTag à votre tableau de bord de surveillance à distance. Pour plus d’informations sur la configuration d’une passerelle et des modules IoT Edge, voir [Concepts relatifs à Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> Dans ce didacticiel, vous utilisez l’éditeur de texte `vi` standard sur l’Intel Nuc. Si vous n’avez pas utilisé `vi` précédemment, vous devez suivre un didacticiel d’introduction, tel que [Unix - Didacticiel de l’éditeur vi][lnk-vi-tutorial], pour vous familiariser avec cet éditeur. Vous pouvez également installer l’éditeur [nano](https://www.nano-editor.org/), plus convivial, à l’aide de la commande `smart install nano -y`.

Ouvrez l’exemple de fichier de configuration dans l’éditeur **vi** à l’aide de la commande suivante :

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic/remote_monitoring.json
```

Recherchez les lignes suivantes dans la configuration pour le module IoTHub :

```json
"args": {
  "IoTHubName": "<<Azure IoT Hub Name>>",
  "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
  "Transport": "http"
}
```

Remplacez les valeurs de l’espace réservé par les informations sur l’IoT Hub que vous avez créées et enregistrées au début de ce tutoriel. La valeur de IoTHubName ressemble à **yourrmsolution37e08** et la valeur de IoTSuffix est généralement **azure-devices.net**.

Recherchez les lignes suivantes dans la configuration du module de mappage :

```json
args": [
  {
    "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Remplacez l’espace réservé **macAddress** par l’adresse MAC du SensorTag, que vous avez notée précédemment. Remplacez les espaces réservés **deviceID** et **deviceKey** par les ID et les clés des deux appareils que vous avez créés précédemment dans la solution de surveillance à distance.

Recherchez les lignes suivantes dans la configuration du module SensorTag :

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  ...
}
```

Remplacez l’espace réservé **device\_mac\_address** par l’adresse MAC du SensorTag, que vous avez notée précédemment.

Enregistrez vos modifications.

Vous pouvez maintenant exécuter la passerelle en utilisant les commandes suivantes :

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/basic
/usr/share/azureiotgatewaysdk/samples/ble_gateway/ble_gateway remote_monitoring.json
```

La passerelle IoT Edge démarre sur l’Intel NUC et envoie la télémétrie du SensorTag à la solution de surveillance à distance :

![La passerelle IoT Edge envoie la télémétrie du SensorTag][img-telemetry]

Appuyez sur **Ctrl-C** pour quitter le programme à tout moment.

## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

La passerelle envoie désormais la télémétrie du dispositif SensorTag à la solution de surveillance à distance. Vous pouvez afficher les données de télémétrie sur le tableau de bord de la solution. Vous pouvez également envoyer des commandes à votre dispositif SensorTag via la passerelle à partir du tableau de bord de la solution.

- Accédez au tableau de bord de la solution.
- Sélectionnez le périphérique que vous avez configuré dans la passerelle, qui représente le SensorTag dans la liste déroulante **Appareil à afficher**.
- La télémétrie du dispositif SensorTag s’affiche sur le tableau de bord.

![Afficher la télémétrie des dispositifs SensorTag][img-telemetry-display]

> [!WARNING]
> Si vous quittez la solution de surveillance à distance en cours d’exécution dans votre compte Azure, vous êtes facturé en fonction du temps d’exécution. Pour plus d’informations sur la manière de réduire votre consommation pendant l’exécution de la solution de surveillance à distance, consultez [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuration des solutions préconfigurées Azure IoT Suite à des fins de démonstration). Supprimez la solution préconfigurée de votre compte Azure lorsque vous avez fini de l’utiliser.


## <a name="next-steps"></a>Étapes suivantes

Visitez le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot/) pour d’autres exemples et de la documentation complémentaire sur Azure IoT.

[img-telemetry]: ./media/iot-suite-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-sensortag/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started
