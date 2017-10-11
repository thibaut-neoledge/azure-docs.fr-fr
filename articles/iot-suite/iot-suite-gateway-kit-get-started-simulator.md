---
title: "Connecter une passerelle à Azure IoT Suite à l’aide d’un Intel NUC | Documents Microsoft"
description: "Utilisez le kit de passerelle commerciale Microsoft IoT et la solution préconfigurée de surveillance à distance. Utilisez la passerelle Azure IoT Edge pour vous connecter à la solution de surveillance à distance, envoyer la télémétrie simulée dans le cloud et répondre aux méthodes appelées à partir du tableau de bord de la solution."
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
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 9ed57d3c23e2adbd42c054f33c8ed46e3d6c9792
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="connect-your-azure-iot-edge-gateway-to-the-remote-monitoring-preconfigured-solution-and-send-simulated-telemetry"></a>Connecter votre passerelle Azure IoT Edge à la solution préconfigurée de surveillance à distance et envoyer la télémétrie simulée

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

Ce didacticiel montre comment utiliser Azure IoT Edge pour simuler des données de température et d’humidité à envoyer à la solution préconfigurée de surveillance à distance. Le tutoriel utilise :

- Azure IoT Edge pour implémenter un exemple de passerelle.
- la solution préconfigurée de surveillance à distance Azure IoT Suite comme serveur principal basé sur le cloud.

## <a name="overview"></a>Vue d’ensemble

Dans ce didacticiel, vous allez effectuer les étapes suivantes :

- Déployer une instance de la solution préconfigurée de surveillance à distance dans votre abonnement Azure. Cette étape déploie et configure automatiquement plusieurs services Azure.
- Configurez votre appareil de passerelle Intel NUC pour qu’il communique avec votre ordinateur et avec la solution de surveillance à distance.
- Configurez la passerelle IoT Edge pour qu’elle envoie la télémétrie simulée que vous pouvez afficher sur le tableau de bord de la solution.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> La solution de surveillance à distance configure un ensemble de services Azure dans votre abonnement Azure. Le déploiement reflète une architecture d’entreprise réelle. Pour éviter des frais de consommation Azure inutiles, supprimez votre instance de la solution préconfigurée dans azureiotsuite.com quand vous ne l’utilisez plus. Si vous avez à nouveau besoin de la solution préconfigurée, vous pouvez la recréer facilement. Pour plus d’informations sur la manière de réduire votre consommation pendant l’exécution de la solution de surveillance à distance, consultez [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuration des solutions préconfigurées Azure IoT Suite à des fins de démonstration).

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

Répétez les étapes précédentes pour ajouter un deuxième appareil à l’aide d’un ID d’appareil tel que **device02**. L’exemple envoie des données à partir de deux appareils simulés dans la passerelle à la solution de surveillance à distance.

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## <a name="build-the-custom-iot-edge-module"></a>Créer le module IoT Edge personnalisé

Vous pouvez maintenant générer le module IoT personnalisé qui permet à la passerelle d’envoyer des messages à la solution de surveillance à distance. Pour plus d’informations sur la configuration d’une passerelle et des modules IoT Edge, voir [Concepts relatifs à Azure IoT Edge][lnk-gateway-concepts].

Téléchargez le code source pour les modules IoT Edge personnalisés à partir de GitHub à l’aide des commandes suivantes :

```bash
cd ~
git clone https://github.com/Azure-Samples/iot-remote-monitoring-c-intel-nuc-gateway-getting-started.git
```

Créez le module IoT Edge personnalisé à l’aide des commandes suivantes :

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
chmod u+x build.sh
sed -i -e 's/\r$//' build.sh
./build.sh
```

Le script de génération place le module IoT Edge personnalisé libsimulator.so dans le dossier de génération.

## <a name="configure-and-run-the-iot-edge-gateway"></a>Configurer et exécuter la passerelle IoT Edge

Vous pouvez maintenant configurer la passerelle IoT Edge pour qu’elle envoie la télémétrie simulée au tableau de bord de la solution de surveillance à distance. Pour plus d’informations sur la configuration d’une passerelle et des modules IoT Edge, consultez [Concepts Azure IoT Edge][lnk-gateway-concepts].

> [!TIP]
> Dans ce didacticiel, vous utilisez l’éditeur de texte `vi` standard sur l’Intel Nuc. Si vous n’avez pas utilisé `vi` précédemment, vous devez suivre un didacticiel d’introduction, tel que [Unix - Didacticiel de l’éditeur vi][lnk-vi-tutorial], pour vous familiariser avec cet éditeur. Vous pouvez également installer l’éditeur [nano](https://www.nano-editor.org/), plus convivial, à l’aide de la commande `smart install nano -y`.

Ouvrez l’exemple de fichier de configuration dans l’éditeur **vi** à l’aide de la commande suivante :

```bash
vi ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator/remote_monitoring.json
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
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  },
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<Azure IoT Hub Device ID>>",
    "deviceKey": "<<Azure IoT Hub Device Key>>"
  }
]
```

Remplacez les espaces réservés **deviceID** et **deviceKey** par les ID et les clés des deux appareils que vous avez créés précédemment dans la solution de surveillance à distance.

Enregistrez vos modifications.

Vous pouvez maintenant exécuter la passerelle IoT Edge en utilisant les commandes suivantes :

```bash
cd ~/iot-remote-monitoring-c-intel-nuc-gateway-getting-started/simulator
/usr/share/azureiotgatewaysdk/samples/simulated_device_cloud_upload/simulated_device_cloud_upload remote_monitoring.json
```

La passerelle démarre sur l’appareil Intel NUC et envoie la télémétrie simulée à la solution de surveillance à distance :

![La passerelle IoT Edge génère la télémétrie simulée][img-simulated telemetry]

Appuyez sur **Ctrl-C** pour quitter le programme à tout moment.

## <a name="view-the-telemetry"></a>Afficher les données de télémétrie

La passerelle IoT Edge envoie à présent la télémétrie simulée à la solution de surveillance à distance. Vous pouvez afficher les données de télémétrie sur le tableau de bord de la solution.

- Accédez au tableau de bord de la solution.
- Sélectionnez un des deux appareils que vous avez configurés dans la passerelle dans la liste déroulante **Appareil à afficher**.
- Les données de télémétrie des appareils de passerelle s’affichent sur le tableau de bord.

![Afficher les données de télémétrie depuis les appareils de passerelle][img-telemetry-display]

> [!WARNING]
> Si vous quittez la solution de surveillance à distance en cours d’exécution dans votre compte Azure, vous êtes facturé en fonction du temps d’exécution. Pour plus d’informations sur la manière de réduire votre consommation pendant l’exécution de la solution de surveillance à distance, consultez [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuration des solutions préconfigurées Azure IoT Suite à des fins de démonstration). Supprimez la solution préconfigurée de votre compte Azure lorsque vous avez fini de l’utiliser.

## <a name="next-steps"></a>Étapes suivantes

Visitez le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot/) pour d’autres exemples et de la documentation complémentaire sur Azure IoT.

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-simulator/appoutput.png

[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-simulator/telemetry.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-gateway-concepts]: https://docs.microsoft.com/azure/iot-hub/iot-hub-linux-iot-edge-get-started