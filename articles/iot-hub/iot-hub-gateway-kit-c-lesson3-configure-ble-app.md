---
title: "Appareil SensorTag et passerelle Azure IoT - Leçon 3 : Exécuter un exemple d’application | Microsoft Docs"
description: "Exécuter un exemple d’application BLE pour recevoir des données à partir du SensorTag BLE et de votre IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "application ble, application de surveillance de capteur, collecte des données de capteur, données de capteurs, données de capteur vers le cloud"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: b33e53a1-1df7-4412-ade1-45185aec5bef
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a9f689c0b231af3cdf9257e2179bf86ff7bc9a31
ms.contentlocale: fr-fr
ms.lasthandoff: 01/25/2017

---
<a id="configure-and-run-a-ble-sample-application" class="xliff"></a>

# Configurer et exécuter l’exemple d’application BLE

<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre

- Clonez l'exemple de référentiel. 
- Configurez la connectivité entre SensorTag et Intel NUC. 
- Utilisez l’interface de ligne de commande Azure pour obtenir vos informations d’IoT Hub et de SensorTag pour un exemple d’application BLE (Bluetooth basse énergie). Configuration et exécution de l’exemple d’application BLE. 

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

<a id="what-you-will-learn" class="xliff"></a>

## Contenu

Cet article portera sur les éléments suivants :

- Configuration et exécution de l’exemple d’application BLE.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin

Vous devez avoir accompli les étapes suivantes :

- [Créer un IoT Hub et inscrire SensorTag](iot-hub-gateway-kit-c-lesson2-register-device.md)

<a id="clone-the-sample-repository-to-the-host-computer" class="xliff"></a>

## Cloner l'exemple de référentiel sur l’ordinateur hôte

Pour cloner l'exemple de référentiel, procédez comme suit sur l’ordinateur hôte :

1. Ouvrez une invite de commande dans Windows ou ouvrez une fenêtre de terminal dans macOS ou Ubuntu.
2. Exécutez les commandes suivantes :

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

<a id="set-up-the-connectivity-between-sensortag-and-intel-nuc" class="xliff"></a>

## Configurer la connectivité entre SensorTag et Intel NUC

Pour configurer la connectivité, procédez comme suit sur l’ordinateur hôte :

1. Initialisez le fichier de configuration en exécutant les commandes suivantes :

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

2. Ouvrez `config-gateway.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

3. Recherchez la ligne de code suivante et remplacez `[device hostname or IP address]` par l’adresse IP ou le nom d'hôte de l’Intel NUC.
   ![capture d’écran de la passerelle de configuration](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

4. Installez les outils d’assistance sur l’Intel NUC en exécutant la commande suivante :

   ```bash
   gulp install-tools
   ```

5. Activez SensorTag en appuyant sur le bouton d’alimentation comme indiqué sur l’image suivante, et le voyant vert clignote.

   ![activer le SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/turn on_off sensortag.jpg)

6. Analysez les appareils SensorTag en exécutant les commandes suivantes :

   ```bash
   gulp discover-sensortag
   ```

7. Testez la connectivité entre le SensorTag et l’Intel NUC en exécutant la commande suivante :

   ```bash
   gulp test-connectivity --mac {mac address}
   ```

   Remplacez `{mac address}` par l’adresse MAC que vous avez obtenue à l’étape précédente.

<a id="get-the-connection-string-of-sensortag" class="xliff"></a>

## Obtention de la chaîne de connexion du SensorTag

Pour obtenir la chaîne de connexion Azure IoT Hub de SensorTag, exécutez la commande suivante sur l’ordinateur hôte :

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` est le nom de l'IoT Hub que vous avez utilisé. Utilisez iot-gateway en tant que valeur de `{resource group name}` et mydevice en tant que valeur de `{device id}` si vous n'avez pas modifié la valeur à la leçon 2.

<a id="configure-the-ble-sample-application" class="xliff"></a>

## Configuration et exécution de l’exemple d’application BLE

Pour configurer et exécuter l’exemple d’application BLE, procédez comme suit sur l’ordinateur hôte :

1. Ouvrez `config-sensortag.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![capture d’écran de configuration sensortag](media/iot-hub-gateway-kit-lessons/lesson3/config_sensortag.png)

2. Effectuez les remplacements suivants dans le code :
   - Remplacez `[IoT hub name]` par le nom de l'IoT Hub que vous avez utilisé.
   - Remplacez `[IoT device connection string]` par la chaîne de connexion SensorTag que vous avez obtenue.
   - Remplacez `[device_mac_address]` par l’adresse MAC de SensorTag que vous avez obtenue.

3. Exécutez l’exemple d’application BLE.

   Pour exécuter l’exemple d’application BLE, procédez comme suit sur l’ordinateur hôte :

   1. Activez le SensorTag.

   2. Déployez et exécutez l’exemple d’application BLE sur l’Intel NUC en exécutant la commande suivante :
   
      ```bash
      gulp run
      ```

<a id="verify-that-the-ble-sample-application-works" class="xliff"></a>

## Vérification du bon fonctionnement de l’exemple d’application BLE

Un résultat similaire à ce qui suit doit s’afficher :

![Résultats de l’exemple d’application BLE](media/iot-hub-gateway-kit-lessons/lesson3/BLE_running.png)

L’exemple d’application assure la collecte des données de température et les envoie à votre IoT Hub. L’exemple d’application se termine automatiquement après l’envoi pendant 40 secondes.

<a id="summary" class="xliff"></a>

## Résumé

Vous avez correctement configuré la connectivité entre le SensorTag et l’Intel NUC et exécuté un exemple d’application BLE qui collecte et envoie des données à partir de SensorTag vers votre IoT Hub. Vous êtes prêt à apprendre à vérifier que votre IoT Hub a reçu les données.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Lire des messages à partir de votre IoT Hub](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)
