---
title: "Connexion d’un Raspberry Pi à Azure IoT Suite à l’aide de Node.js avec des capteurs réels | Microsoft Docs"
description: "Utilisez le Kit de démarrage Microsoft Azure IoT pour le Raspberry Pi 3 et IoT Azure Suite. Utiliser Node.js pour connecter votre Raspberry Pi à la solution de surveillance à distance, envoyer la télémétrie des capteurs dans le cloud et répondre aux méthodes appelées à partir du tableau de bord de la solution."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 6d8e6ef070c3b9d9623f4b29ab97a5d20f69d499
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017

---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-nodejs"></a>Connecter votre Raspberry Pi 3 à la solution de surveillance à distance et envoyer la télémétrie depuis un capteur réel à l’aide de Node.js

[!INCLUDE [iot-suite-raspberry-pi-kit-selector](../../includes/iot-suite-raspberry-pi-kit-selector.md)]

Ce tutoriel vous montre comment utiliser le Kit de démarrage Microsoft Azure IoT pour Raspberry Pi 3 pour développer un lecteur de température et d’humidité capable de communiquer avec le cloud. Le tutoriel utilise :

- le système d’exploitation Raspbian, le langage de programmation Node.js et le Kit de développement logiciel (SDK) d’appareil Microsoft Azure IoT pour Node.js pour implémenter un exemple d’appareil ;
- la solution préconfigurée de surveillance à distance Azure IoT Suite comme serveur principal basé sur le cloud.

## <a name="overview"></a>Vue d’ensemble

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

- Déployer une instance de la solution préconfigurée de surveillance à distance dans votre abonnement Azure. Cette étape déploie et configure automatiquement plusieurs services Azure.
- Configurer votre appareil pour qu’il communique avec votre ordinateur et avec la solution de surveillance à distance.
- Mettre à jour l’exemple de code d’appareil pour se connecter à la solution de surveillance à distance et envoyer les données de télémétrie que vous pouvez afficher sur le tableau de bord de la solution.

[!INCLUDE [iot-suite-raspberry-pi-kit-prerequisites](../../includes/iot-suite-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> La solution de surveillance à distance configure un ensemble de services Azure dans votre abonnement Azure. Le déploiement reflète une architecture d’entreprise réelle. Pour éviter des frais de consommation Azure inutiles, supprimez votre instance de la solution préconfigurée dans azureiotsuite.com quand vous ne l’utilisez plus. Si vous avez à nouveau besoin de la solution préconfigurée, vous pouvez la recréer facilement. Pour plus d’informations sur la manière de réduire votre consommation pendant l’exécution de la solution de surveillance à distance, consultez [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuration des solutions préconfigurées Azure IoT Suite à des fins de démonstration).

[!INCLUDE [iot-suite-raspberry-pi-kit-view-solution](../../includes/iot-suite-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Vous pouvez à présent télécharger et configurer l’application cliente de surveillance à distance sur votre Raspberry Pi.

### <a name="install-nodejs"></a>Installer Node.js

Installez Node.js sur votre Raspberry Pi. Le kit de développement logiciel (SDK) IoT pour Node.js requiert la version 0.11.5 de Node.js ou une version ultérieure. Les étapes suivantes vous montrent comment installer Node.js v6.10.2 sur votre Raspberry Pi :

1. Pour mettre à jour votre Raspberry Pi, utilisez la commande suivante :

    ```sh
    sudo apt-get update
    ```

1. Pour télécharger les fichiers binaires Node.js sur votre Raspberry Pi, utilisez la commande suivante :

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Pour installer les binaires, utilisez la commande suivante :

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Pour vérifier que Node.js v6.10.2 a été installé avec succès, utilisez la commande suivante :

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Cloner les dépôts

Si vous n’avez pas déjà fait, clonez les dépôts requis en exécutant les commandes suivantes sur votre Pi :

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git`
```

### <a name="update-the-device-connection-string"></a>Mettre à jour la chaîne de connexion d’appareil

Ouvrez l’exemple de fichier source dans l’éditeur **nano** à l’aide de la commande suivante :

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

Recherchez la ligne :

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Remplacez les valeurs dans l’espace réservé par les informations sur l’appareil et l’IoT Hub que vous avez créées et enregistrées au début de ce tutoriel. Enregistrez vos modifications (**Ctrl-O**, **Entrée**) et quittez l’éditeur (**Ctrl-X**).

## <a name="run-the-sample"></a>Exécution de l'exemple

Exécutez les commandes suivantes pour installer les packages requis pour l’exemple :

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic
npm install
```

Vous pouvez maintenant exécuter l’exemple de programme sur le Raspberry Pi. Entrez la commande :

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

La sortie suivante est un exemple de sortie qui peut s’afficher à l’invite de commandes sur le Raspberry Pi :

![Sortie de l’application de Raspberry Pi][img-raspberry-output]

Appuyez sur **Ctrl-C** pour quitter le programme à tout moment.

[!INCLUDE [iot-suite-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Étapes suivantes

Visitez le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot/) pour d’autres exemples et de la documentation complémentaire sur Azure IoT.

[img-raspberry-output]: ./media/iot-suite-raspberry-pi-kit-node-get-started-basic/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

