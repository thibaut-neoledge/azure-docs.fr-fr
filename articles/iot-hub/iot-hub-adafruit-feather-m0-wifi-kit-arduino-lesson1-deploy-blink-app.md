---
title: "Connecter Arduino à Azure IoT - Leçon 1 : Déployer une application | Microsoft Docs"
description: "Clonez l’exemple d’application Arduino à partir de GitHub et exécutez gulp pour déployer cette application sur votre carte Adafruit Feather M0 WiFi. Cet exemple d’application fait clignoter la LED GPIO"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: projets de led arduino, clignotement de led arduino, code de clignotement de led arduino, programme de clignotement arduino, exemple de clignotement arduino
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: b0a7d076-d580-4686-9f7d-c0712750b615
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a2058611cecb33eab04de9249ad73b1c4ec6c2a5
ms.contentlocale: fr-fr
ms.lasthandoff: 01/24/2017

---
<a id="create-and-deploy-the-blink-application" class="xliff"></a>

# Créer et déployer l’application blink
<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre
Clonez l’exemple d’application Arduino à partir de GitHub et utilisez l'outil gulp pour déployer l'exemple d'application sur votre carte Adafruit Feather M0 WiFi Arduino. Avec l'exemple d’application, la LED embarquée GPIO #13 clignote toutes les deux secondes.

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting-page].

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
* Comment déployer et exécuter l’exemple d’application sur votre carte Arduino.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin
Vous devez avoir terminé les opérations suivantes :

* [Configuration de votre appareil][configure-your-device]
* [Obtenir les outils][get-the-tools]

<a id="open-the-sample-application" class="xliff"></a>

## Ouvrir l’exemple d’application
Procédez comme suit pour ouvrir l’exemple d’application :

1. Clonez l’exemple de référentiel à partir de GitHub en exécutant la commande suivante :

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-feather-m0-getting-started.git
   ```
2. Ouvrez l’exemple d’application dans Visual Studio Code en exécutant les commandes suivantes :

   ```bash
   cd iot-hub-c-feather-m0-getting-started
   cd Lesson1
   code .
   ```

   ![Structure du référentiel][repo-structure]

Le fichier `app.ino` dans le sous-dossier `app` est le fichier source clé qui contient le code pour contrôler la LED.

<a id="install-application-dependencies" class="xliff"></a>

### Installation des dépendances de l’application
Installez les bibliothèques et d’autres modules dont vous avez besoin pour l’exemple d’application en exécutant la commande suivante :

```bash
npm install
```

<a id="configure-the-device-connection" class="xliff"></a>

## Configuration de la connexion de l’appareil
Procédez comme suit pour configurer la connexion de l’appareil :

1. Obtenez le port série de l'appareil à l'aide de l’interface de ligne de commande de découverte :

   ```bash
   devdisco list --usb
   ```

   Vous devriez obtenir un résultat semblable à ce qui suit et identifier le port USB COM de votre carte Arduino : ![Découverte de l’appareil][device-discovery]

2. Ouvrez le fichier `config.json` dans le dossier de la leçon puis ajoutez la valeur du numéro de port COM trouvé :

   ```json
   {
       "device_port" : "COM1"
   }
   ```
   ![config.json][config-json]
   > [!NOTE]
   > Sur la plate-forme Windows, le port COM le format `COM1, COM2, ...`. Sur macOS ou Ubuntu, il commence par `/dev/`.

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

## Déploiement et exécution de l’exemple d’application
<a id="install-the-required-tools-for-your-arduino-board" class="xliff"></a>

### Installation des outils requis pour votre carte Arduino

Installez le kit de développement logiciel (SDK) Azure IoT Hub sur votre carte Arduino en exécutant la commande suivante :

```bash
gulp install-tools
```

Cette tâche peut prendre beaucoup de temps en fonction de votre connexion réseau.

> [!NOTE]
> Veuillez quitter l’instance Arduino IDE en cours lors de l’exécution des tâches gulp : `install-tools`, `run`.

<a id="deploy-and-run-the-sample-app" class="xliff"></a>

### Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application en exécutant la commande suivante :

```bash
gulp run

# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

<a id="verify-the-app-works" class="xliff"></a>

### Vérification du bon fonctionnement de l’application
Si vous ne voyez pas la LED clignoter, consultez le [guide de dépannage][troubleshooting-page] des problèmes courants.

![LED clignotante][led-blinking]

<a id="summary" class="xliff"></a>

## Résumé
Vous avez installé les outils nécessaires pour travailler avec votre carte Arduino et déployé un exemple d’application sur votre carte Arduino pour faire clignoter la LED. Vous pouvez maintenant créer, déployer et exécuter un autre exemple d’application qui connecte votre carte Arduino à Azure IoT Hub pour envoyer et recevoir des messages.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Obtenir les outils Azure][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting-page]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-blink-arduino-mac.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[led-blinking]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
