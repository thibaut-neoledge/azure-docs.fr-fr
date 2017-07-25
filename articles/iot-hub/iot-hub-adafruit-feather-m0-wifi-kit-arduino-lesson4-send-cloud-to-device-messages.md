---
title: "Connecter Arduino Pi (C) à Azure IoT - Leçon 4 : Cloud-à-appareil | Microsoft Docs"
description: "Un exemple d’application s’exécute sur la carte Adafruit Feather M0 WiFi et surveille les messages entrants à partir de votre IoT Hub. Une nouvelle tâche gulp envoie des messages à la carte Adafruit Feather M0 WiFi à partir de votre IoT Hub pour faire clignoter la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "contrôle de la led avec arduino à partir du web, contrôle de la led avec arduino via le web"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: a0bf53fb-29fb-485f-ba4a-6c715057b1a2
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f9feb665ac93028023df86889ca53a64ac2ec0e
ms.openlocfilehash: 63113841ca836681232e0aa43b15b444f8bb92e2
ms.contentlocale: fr-fr
ms.lasthandoff: 01/28/2017

---
<a id="run-a-sample-application-to-receive-cloud-to-device-messages" class="xliff"></a>

# Exécution d’un exemple d’application pour recevoir des messages cloud-à-appareil
Dans cet article, vous déployez un exemple d’application sur votre carte Adafruit Feather M0 WiFi Arduino.

L’exemple d’application surveille les messages entrants à partir de votre IoT Hub. Vous exécutez également une tâche gulp sur votre ordinateur pour envoyer des messages à votre carte Arduino à partir de votre IoT Hub. À la réception des messages, l’exemple d’application fait clignoter la LED. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre
* Connectez l’exemple d’application à votre IoT Hub.
* Déployez et exécutez l'exemple d'application.
* Envoyez des messages depuis votre IoT Hub vers votre carte Arduino pour faire clignoter la LED.

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
Cet article portera sur les éléments suivants :
* Surveillance des messages entrants à partir de votre IoT Hub.
* Envoi de messages cloud-à-appareil depuis votre IoT Hub vers votre carte Arduino.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin
* Votre carte Arduino, configurée et prête à l’utilisation. Pour savoir comment configurer votre carte Arduino, consultez [Configuration de votre appareil][configure-your-device].
* Un IoT Hub créé dans le cadre de votre abonnement Azure. Pour savoir comment créer votre Azure IoT Hub, consultez [Création de votre Azure IoT Hub][create-your-azure-iot-hub].

<a id="connect-the-sample-application-to-your-iot-hub" class="xliff"></a>

## Connexion de l’exemple d’application à votre IoT Hub

1. Assurez-vous que vous êtes dans le dossier du référentiel `iot-hub-c-feather-m0-getting-started`.

   Ouvrez l’exemple d’application dans Visual Studio Code en exécutant les commandes suivantes :

   ```bash
   cd Lesson4
   code .
   ```

   Notez la présence du fichier `app.ino` dans le sous-dossier `app`. Le fichier `app.ino` est le fichier source clé qui contient le code permettant de surveiller les messages entrants à partir du IoT Hub. La fonction `blinkLED` fait clignoter la LED.

   ![Structure de référentiel dans l’exemple d’application][repo-structure]

2. Obtenez le port série de l’appareil à l’aide de l’interface de ligne de commande de découverte :

   ```bash
   devdisco list --usb
   ```

   Vous devriez obtenir un résultat semblable à ce qui suit et identifier le port USB COM de votre carte Arduino :

   ![Découverte de l’appareil][device-discovery]

3. Ouvrez le fichier `config.json` dans le dossier de la leçon et entrez la valeur du numéro de port COM trouvé :

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Sur la plateforme Windows, le port COM a le format `COM1, COM2, ...`. Sur macOS ou Ubuntu, il commence par `/dev/`.

4. Initialisez le fichier de configuration en exécutant les commandes suivantes :

   ```bash
   # For Windows command prompt
   npm install
   gulp init
   gulp install-tools
   ```

5. Dans le fichier `config-arduino.json`, effectuez les remplacements suivants :

   Si vous avez effectué les étapes de la section [Créer une application de fonction Azure et un compte de stockage][create-an-azure-function-app-and-storage-account] sur cet ordinateur, toutes les configurations sont héritées, donc vous pouvez passer à la tâche de déploiement et d’exécution de l’exemple d’application. Si vous avez effectué les étapes de la section [Créer une application de fonction Azure et un compte de stockage][create-an-azure-function-app-and-storage-account] sur un autre ordinateur, vous devez remplacer les espaces réservés dans le fichier `config-arduino.json`. Le fichier `config-arduino.json` se trouve dans le sous-dossier de votre dossier racine.

   ![Contenu du fichier config-arduino.json][config-arduino-json]

   * Remplacez **[Wi-Fi SSID]** avec votre SSID Wi-Fi connecté à Internet.
   * Remplacez **[Wi-Fi password]** par votre mot de passe Wi-Fi. Supprimez la chaîne si votre réseau Wi-Fi ne requiert aucun mot de passe.
   * Remplacez **[chaîne de connexion d’appareil IoT]** par la chaîne de connexion d’appareil que vous obtenez en exécutant la commande `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Remplacez **[chaîne de connexion d’IoT Hub]** par la chaîne de connexion IoT Hub que vous obtenez en exécutant la commande `az iot hub show-connection-string --name {my hub name}`.

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

## Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application sur votre carte Arduino en exécutant les commandes suivantes :

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

La commande gulp déploie l’exemple d’application sur votre carte Arduino. Ensuite, elle exécute l’application sur votre carte Arduino et une tâche distincte sur votre ordinateur hôte afin d’envoyer 20 messages de clignotement à votre carte Arduino à partir de votre IoT Hub.

Une fois l’exemple d’application exécuté, il commence à écouter les messages à partir de votre IoT Hub. Pendant ce temps, la tâche gulp envoie plusieurs messages de « clignotement » à partir de votre IoT Hub vers votre carte Arduino. Pour chaque message de clignotement reçu par la carte, l’exemple d’application demande à la fonction `blinkLED` de faire clignoter la LED.

La LED doit clignoter toutes les deux secondes tandis que la tâche gulp envoie 20 messages de votre IoT Hub vers votre carte Arduino. Le dernier message est un message « stop » qui interrompt l’exécution de l’application.

![Exemple d’application avec la commande gulp et les messages de clignotement][sample-application]

<a id="summary" class="xliff"></a>

## Résumé
Vous avez correctement envoyé des messages à partir de votre IoT Hub vers votre carte Arduino pour faire clignoter la LED. La tâche suivante est facultative : modifier le comportement activé/désactivé de la LED.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Modification du comportement activé/désactivé de la LED][change-the-on-and-off-led-behavior]


<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/repo_structure_arduino.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[create-an-azure-function-app-and-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/config-arduino.png
[sample-application]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_blink_arduino.png
[change-the-on-and-off-led-behavior]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
