---
featureFlags:
- usabilla
title: "Connecter Raspberry Pi (Node) à Azure IoT - Leçon 4 : Cloud-à-appareil | Microsoft Docs"
description: "L’exemple d’application s’exécute sur Pi et surveille les messages entrants à partir de votre IoT Hub. Une nouvelle tâche gulp envoie des messages à Pi à partir de votre IoT Hub pour faire clignoter la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "cloud-à-appareil, message du cloud"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: 6ae6539e-1163-4490-8d72-fdf7803e3054
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 92ee9d6faae9f539c663395e47714609a146f2df
ms.lasthandoff: 01/24/2017


---
# <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Exécution de l’exemple d’application pour recevoir des messages cloud-à-appareil
Dans cet article, vous déployez un exemple d’application sur Raspberry Pi 3. L’exemple d’application surveille les messages entrants à partir de votre IoT Hub. Vous exécutez également une tâche gulp sur votre ordinateur pour envoyer des messages à Pi à partir de votre IoT Hub. À la réception des messages, l’exemple d’application fait clignoter la LED. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-do"></a>Procédure à suivre
* Connectez l’exemple d’application à votre IoT Hub.
* Déployez et exécutez l'exemple d'application.
* Envoyez des messages à partir votre IoT Hub vers Pi pour faire clignoter la LED.

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :
* Surveillance des messages entrants à partir de votre IoT Hub
* Envoi de messages cloud-à-appareil à partir de votre IoT Hub vers Pi.

## <a name="what-you-need"></a>Ce dont vous avez besoin
* Raspberry Pi 3, configuré pour utilisation. Pour savoir comment configurer Pi, consultez [Configuration de votre appareil](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md).
* Un IoT Hub créé dans le cadre de votre abonnement Azure. Pour savoir comment créer votre IoT Hub, consultez [Création de votre IoT Hub et inscription de Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).

## <a name="connect-the-sample-application-to-your-iot-hub"></a>Connexion de l’exemple d’application à votre IoT Hub
1. Assurez-vous que vous êtes dans le dossier du référentiel `iot-hub-node-raspberrypi-getting-started`. Ouvrez l’exemple d’application dans Visual Studio Code en exécutant les commandes suivantes :
   
   ```bash
   cd Lesson4
   code .
   ```
   
   Notez la présence du fichier `app.js` dans le sous-dossier `app`. Le fichier `app.js` est le fichier source clé qui contient le code permettant de surveiller les messages entrants à partir du IoT Hub. La fonction `blinkLED` fait clignoter la LED.
   
   ![Structure de référentiel dans l’exemple d’application](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)
2. Initialisez le fichier de configuration à l’aide des commandes suivantes :
   
   ```bash
   npm install
   gulp init
   ```
   
   Si vous avez effectué les étapes de la section [Créer une application de fonction Azure et un compte de stockage](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md) sur cet ordinateur, toutes les configurations sont héritées, donc vous pouvez passer à la tâche de déploiement et d’exécution de l’exemple d’application. Si vous avez effectué les étapes de la section [Créer une application de fonction Azure et un compte de stockage](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md) sur un autre ordinateur, vous devez remplacer les espaces réservés dans le fichier `config-raspberrypi.json`. Le fichier `config-raspberrypi.json` se trouve dans le sous-dossier de votre dossier racine.
   
   ![Contenu du fichier config-raspberrypi.json](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

* Remplacez **[nom d’hôte ou adresse IP de l’appareil]** par l’adresse IP ou le nom d’hôte de Pi que vous obtenez en exécutant la commande `devdisco list --eth`.
* Remplacez **[chaîne de connexion d’appareil IoT]** par la chaîne de connexion d’appareil que vous obtenez en exécutant la commande `az iot device show-connection-string --hub-name {my hub name} --device-id {device id} -g iot-sample {resource group name}`.
* Remplacez **[chaîne de connexion d’IoT Hub]** par la chaîne de connexion IoT Hub que vous obtenez en exécutant la commande `az iot hub show-connection-string --name {my hub name} -g iot-sample {resource group name}`.

> [!NOTE]
> Exécutez également **gulp install-tools**, si vous ne l’avez pas fait dans la leçon 1.

## <a name="deploy-and-run-the-sample-application"></a>Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application sur Pi en exécutant la commande suivante :

```bash
gulp deploy && gulp run
```

La commande déploie l’exemple d’application sur Pi. Ensuite, elle exécute l’application sur Pi et une tâche distincte sur votre ordinateur hôte afin d’envoyer 20 messages de clignotement à Pi à partir de votre IoT Hub.

Une fois l’exemple d’application exécuté, il commence à écouter les messages à partir de votre IoT Hub. Pendant ce temps, la tâche gulp envoie plusieurs messages de « clignotement » à partir de votre IoT Hub vers Pi. Pour chaque message de clignotement reçu par Pi, l’exemple d’application demande à la fonction `blinkLED` de faire clignoter la LED.

La LED doit clignoter toutes les deux secondes tandis que la tâche gulp envoie 20 messages de votre IoT Hub vers Pi. Le dernier message est un message « stop » qui indique à l’application d’interrompre l’exécution.

![Exemple d’application avec la commande gulp et les messages de clignotement](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="summary"></a>Résumé
Vous avez correctement envoyé des messages à partir de votre IoT Hub vers Pi pour faire clignoter la LED. La tâche suivante est facultative : modifier le comportement activé/désactivé de la LED.

## <a name="next-steps"></a>Étapes suivantes
[Modification du comportement activé/désactivé de la LED](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


