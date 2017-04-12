---
title: "Appareil simulé et passerelle Azure IoT - Leçon 3 : Exécuter un exemple d’application | Microsoft Docs"
description: "Exécuter un exemple d’application d&quot;appareil simulé pour envoyer des données de température vers Azure IoT Hub"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "données vers le cloud"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 5d051d99-9749-4150-b3c8-573b0bda9c52
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 873f3cef8cb1d115f77f0d3fa2c4b50391f66d91
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-simulated-device-sample-app"></a>Configurer et exécuter un exemple d’application d’appareil simulé

## <a name="what-you-will-do"></a>Procédure à suivre

- Clonez l'exemple de référentiel.
- Utilisez l’interface de ligne de commande Azure afin d'obtenir les informations sur votre IoT Hub et sur l'appareil logique pour l’exemple d’application d'appareil simulé. Configurez et exécutez l'exemple d’application d’appareil simulé.

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu

Cet article portera sur les éléments suivants :

- Comment configurer et exécuter l'exemple d’application d’appareil simulé.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Vous devez avoir accompli avec succès les étapes

- [Créer un hub IoT et enregistrer votre appareil](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>Cloner l'exemple de référentiel sur l’ordinateur hôte

Pour cloner l'exemple de référentiel, procédez comme suit sur l’ordinateur hôte :

1. Ouvrez une invite de commande dans Windows ou ouvrez une fenêtre de terminal dans macOS ou Ubuntu.
2. Exécutez les commandes suivantes :

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="configure-the-simulated-device-and-your-nuc"></a>Configurer l'appareil simulé et votre NUC

1. Ouvrez le fichier de configuration `config.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   code config.json
   ```

2. Remplacer `"has_sensortag": true` par `"has_sensortag": false`

   ![Configuration si vous n’avez pas d'appareil TI SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/config_no_sensortag.png)

3. Initialisez le fichier de configuration en exécutant les commandes suivantes :

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

4. Ouvrez `config-gateway.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

5. Recherchez la ligne de code suivante et remplacez `[device hostname or IP address]` par l’adresse IP ou le nom d'hôte de la NUC Intel.
   ![capture d’écran de la passerelle de configuration](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

## <a name="get-the-connection-string-of-your-iot-hub-logical-device"></a>Obtenir la chaîne de connexion de l'appareil logique de votre IoT Hub

Pour obtenir la chaîne de connexion Azure IoT Hub de votre appareil logique, exécutez la commande suivante sur l’ordinateur hôte :

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` est le nom de l'IoT Hub que vous avez utilisé. Utilisez iot-gateway en tant que valeur de `{resource group name}` et mydevice en tant que valeur de `{device id}` si vous n'avez pas modifié la valeur à la leçon 2.

## <a name="configure-the-simulated-device-cloud-upload-sample-application"></a>Configurer l'exemple d’application de téléchargement cloud d’appareil simulé

Pour configurer et exécuter l’exemple d’application de téléchargement cloud d'appareil simulé, procédez comme suit sur l’ordinateur hôte :

1. Ouvrez `config-sensortag.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![capture d’écran de configuration sensortag](media/iot-hub-gateway-kit-lessons/lesson3/config_simulated_device.png)

2. Effectuez les remplacements suivants dans le code :
   - Remplacez `[IoT hub name]` par le nom de l'IoT Hub.
   - Remplacez `[IoT device connection string]` par la chaîne de connexion de l'appareil logique de votre IoT Hub.

3. Exécutez l'application.

   Déployez et exécutez l’application en exécutant la commande suivante :

   ```bash
   gulp run
   ```

## <a name="verify-the-sample-application-works"></a>Vérifier le fonctionnement de l’exemple d’application

Un résultat similaire à ce qui suit s’affiche normalement :

![résultat de l'exemple d'application d'appareil simulé](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_simudev.png)

L’application envoie les données de température à votre IoT Hub, opération qui dure 40 secondes.

## <a name="summary"></a>Résumé

Vous avez correctement configuré et exécuté l'exemple d'application de téléchargement cloud d'appareil simulé qui envoie des données à votre IoT Hub avec appareil simulé.

## <a name="next-steps"></a>Étapes suivantes
[Lire des messages à partir de votre IoT Hub](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)
