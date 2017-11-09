---
title: "Connexion d’un Raspberry Pi à Azure IoT Suite à l’aide du langage de programmation C pour prendre en charge les mises à jour du microprogramme | Microsoft Docs"
description: "Utilisez le Kit de démarrage Microsoft Azure IoT pour le Raspberry Pi 3 et IoT Azure Suite. Utiliser C pour connecter votre Raspberry Pi à la solution de surveillance à distance, envoyer la télémétrie des capteurs dans le cloud et effectuer une mise à jour du microprogramme à distance."
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 8160752b0116c3ef3e6b6ab7920bb35e471f180b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-c"></a>Connexion de votre Raspberry Pi 3 à la solution de surveillance à distance et activation des mises à jour de microprogramme à distance à l’aide de C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Ce tutoriel vous montre comment utiliser le Kit de démarrage Microsoft Azure IoT pour Raspberry Pi 3 pour :

* développer un lecteur de température et d’humidité capable de communiquer avec le cloud ;
* activer et effectuer une mise à jour à distance du microprogramme pour mettre à jour l’application cliente sur le Raspberry Pi.

Le tutoriel utilise :

* le système d’exploitation Raspbian, le langage de programmation C et le kit de développement logiciel (SDK) Microsoft Azure IoT pour C pour implémenter un exemple d’appareil ;
* la solution préconfigurée de surveillance à distance Azure IoT Suite comme serveur principal basé sur le cloud.

## <a name="overview"></a>Vue d’ensemble

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

* Déployer une instance de la solution préconfigurée de surveillance à distance dans votre abonnement Azure. Cette étape déploie et configure automatiquement plusieurs services Azure.
* Configurer votre appareil pour qu’il communique avec votre ordinateur et avec la solution de surveillance à distance.
* Mettre à jour l’exemple de code d’appareil pour se connecter à la solution de surveillance à distance et envoyer les données de télémétrie que vous pouvez afficher sur le tableau de bord de la solution.
* Utiliser l’exemple de code d’appareil pour mettre à jour l’application cliente.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> La solution de surveillance à distance configure un ensemble de services Azure dans votre abonnement Azure. Le déploiement reflète une architecture d’entreprise réelle. Pour éviter des frais de consommation Azure inutiles, supprimez votre instance de la solution préconfigurée dans azureiotsuite.com quand vous ne l’utilisez plus. Si vous avez à nouveau besoin de la solution préconfigurée, vous pouvez la recréer facilement. Pour plus d’informations sur la manière de réduire votre consommation pendant l’exécution de la solution de surveillance à distance, consultez [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuration des solutions préconfigurées Azure IoT Suite à des fins de démonstration).

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Télécharger et configurer l’exemple

Vous pouvez à présent télécharger et configurer l’application cliente de surveillance à distance sur votre Raspberry Pi.

### <a name="clone-the-repositories"></a>Cloner les dépôts

Si vous ne l’avez pas déjà fait, clonez les dépôts requis en exécutant les commandes suivantes sur votre Pi :

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Mettre à jour la chaîne de connexion d’appareil

Ouvrez l’exemple de fichier de configuration dans l’éditeur **nano** à l’aide de la commande suivante :

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Remplacez les valeurs dans l’espace réservé par les informations sur l’appareil et l’IoT Hub que vous avez créées et enregistrées au début de ce tutoriel.

Lorsque vous avez terminé, le contenu du fichier deviceinfo doit ressembler à l’exemple suivant :

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Enregistrez vos modifications (**Ctrl-O**, **Entrée**) et quittez l’éditeur (**Ctrl-X**).

## <a name="build-the-sample"></a>Générer l’exemple

Si vous ne l’avez pas déjà fait, installez les packages requis pour le kit de développement logiciel (SDK) d’appareil Microsoft Azure IoT pour C en exécutant les commandes suivantes dans un terminal sur le Raspberry Pi :

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Vous pouvez maintenant créer l’exemple de solution sur le Raspberry Pi :

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
```

Vous pouvez maintenant exécuter l’exemple de programme sur le Raspberry Pi. Entrez la commande :

  ```sh
  sudo ~/cmake/remote_monitoring/remote_monitoring
  ```

La sortie suivante est un exemple de sortie qui peut s’afficher à l’invite de commandes sur le Raspberry Pi :

![Sortie de l’application de Raspberry Pi][img-raspberry-output]

Appuyez sur **Ctrl-C** pour quitter le programme à tout moment.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. Dans le tableau de bord de la solution, cliquez sur **Appareils** pour accéder à la page **Appareils**. Sélectionnez votre Raspberry Pi dans la **Liste des appareils**. Ensuite, choisissez **Méthodes** :

    ![Liste des appareils dans le tableau de bord][img-list-devices]

1. Sur la page **Appeler une méthode**, choisissez **InitiateFirmwareUpdate** dans la liste déroulante **Méthode**.

1. Dans le champ **FWPackageURI**, entrez **https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit/raw/master/advanced/2.0/package/remote_monitoring.zip**. Ce fichier d’archive contient l’implémentation de la version 2.0 du microprogramme.

1. Choisissez **InvokeMethod**. L’application sur le Raspberry Pi envoie un accusé de réception au tableau de bord de la solution. Elle démarre ensuite le processus de mise à jour du microprogramme en téléchargeant la nouvelle version de ce dernier :

    ![Afficher l’historique de la méthode][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Observer le microprogramme des mises à jour

Vous pouvez observer le processus de mise à jour du microprogramme en cours d’exécution sur l’appareil et en affichant les propriétés déclarées dans le tableau de bord de solution :

1. Vous pouvez afficher la progression du processus de mise à jour sur le Raspberry Pi :

    ![Afficher la progression de la mise à jour][img-update-progress]

    > [!NOTE]
    > L’application de surveillance à distance redémarre en mode silencieux à l’issue de la mise à jour. Utilisez la commande `ps -ef` pour vérifier qu’elle est en cours d’exécution. Si vous souhaitez mettre fin au processus, utilisez la commande `kill` avec l’ID de processus.

1. Vous pouvez afficher l’état de la mise à jour du microprogramme, comme indiqué par l’appareil, dans le portail de la solution. La capture d’écran suivante montre l’état et la durée de chaque étape du processus de mise à jour, ainsi que la nouvelle version du microprogramme :

    ![Afficher l’état du travail][img-job-status]

    Si vous accédez de nouveau au tableau de bord, vous pouvez vérifier que l’appareil continue d’envoyer la télémétrie après la mise à jour du microprogramme.

> [!WARNING]
> Si vous quittez la solution de surveillance à distance en cours d’exécution dans votre compte Azure, vous êtes facturé en fonction du temps d’exécution. Pour plus d’informations sur la manière de réduire votre consommation pendant l’exécution de la solution de surveillance à distance, consultez [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuration des solutions préconfigurées Azure IoT Suite à des fins de démonstration). Supprimez la solution préconfigurée de votre compte Azure lorsque vous avez fini de l’utiliser.

## <a name="next-steps"></a>Étapes suivantes

Visitez le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot/) pour d’autres exemples et de la documentation complémentaire sur Azure IoT.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md