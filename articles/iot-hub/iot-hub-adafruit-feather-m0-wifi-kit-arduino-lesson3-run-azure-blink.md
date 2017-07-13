---
title: "Connecter Arduino (C) à Azure IoT - Leçon 3 : Exécuter les exemples | Microsoft Docs"
description: "Déployez et exécutez sur Adafruit Feather M0 WiFi un exemple d’application qui envoie des messages à votre IoT Hub et fait clignoter la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "service cloud iot, envoi de données au cloud arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 92cce319-2b17-4c9b-889d-deac959e3e7c
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 631a6677e4801b1c3475b9e8f2726219dd101132
ms.contentlocale: fr-fr
ms.lasthandoff: 01/24/2017

---
<a id="run-a-sample-application-to-send-device-to-cloud-messages" class="xliff"></a>

# Exécution d’un exemple d’application pour envoyer des messages appareil-à-cloud
<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre
Cet article vous explique comment déployer et exécuter sur votre carte Adafruit Feather M0 WiFi Arduino un exemple d’application qui envoie des messages à votre IoT Hub.

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
Vous apprendrez à utiliser l’outil gulp pour déployer et exécuter l’exemple d’application Arduino sur votre carte Arduino.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin
* Avant de commencer cette tâche, vous devez avoir correctement suivi la section [Création d’une application de fonction Azure et d’un compte de stockage pour traiter et stocker des messages du IoT Hub][process-and-store-iot-hub-messages].

<a id="get-your-iot-hub-and-device-connection-strings" class="xliff"></a>

## Obtenir vos chaînes de connexion d’IoT Hub et d’appareil
La chaîne de connexion de l’appareil permet de connecter votre carte Arduino à votre IoT Hub. La chaîne de connexion de l’IoT Hub permet de connecter votre IoT Hub à l’identité d’appareil représentant votre carte Arduino dans l’IoT Hub.

* Répertorier tous les IoT Hubs de votre groupe de ressources en exécutant la commande suivante de l’interface de ligne de commande Azure :

```bash
az iot hub list -g iot-sample --query [].name
```

Si vous n’avez pas modifié la valeur, utilisez `iot-sample` en tant que valeur de `{resource group name}`.

* Obtenez la chaîne de connexion de l’IoT Hub en exécutant la commande de l’interface de ligne de commande Azure suivante :

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` est le nom que vous avez spécifié lorsque vous avez créé votre IoT Hub et inscrit votre carte Arduino.

* Obtenez la chaîne de connexion de l’appareil en exécutant la commande suivante :

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id mym0wifi
```

Si vous n’avez pas modifié la valeur, utilisez `mym0wifi` en tant que valeur de `{device id}`.
<a id="configure-the-device-connection" class="xliff"></a>

## Configuration de la connexion de l’appareil
Procédez comme suit pour configurer la connexion de l’appareil :

1. Obtenez le port série de l'appareil à l'aide de l’interface de ligne de commande de découverte :

   ```bash
   devdisco list --usb
   ```

   Vous devriez obtenir un résultat semblable à ce qui suit et identifier le port USB COM de votre carte Arduino :

   ![Découverte de l’appareil][device-discovery]

2. Ouvrez le fichier `config.json` dans le dossier de la leçon puis ajoutez la valeur du numéro de port COM trouvé :

   ```json
   {
       "device_port" : "COM1"
   }
   ```

   ![config.json][config-json]

   > [!NOTE]
   > Sur la plate-forme Windows, le port COM le format `COM1, COM2, ...`. Sur macOS ou Ubuntu, il commence par `/dev/`.

3. Initialisez le fichier de configuration en exécutant les commandes suivantes :

   ```bash
   npm install
   gulp init
   gulp install-tools
   ```
4. Ouvrez le fichier de configuration de l’appareil `config-arduino.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```

   ![config-arduino.json][config-arduino-json]

5. Dans le fichier `config-arduino.json`, effectuez les remplacements suivants :

   * Remplacez **[Wi-Fi SSID]** avec votre SSID Wi-Fi connecté à Internet.
   * Remplacez **[Wi-Fi password]** par votre mot de passe Wi-Fi. Supprimez la chaîne si votre réseau Wi-Fi ne requiert aucun mot de passe.
   * Remplacez **[chaîne de connexion de l’appareil IoT]** par la `device connection string` obtenue.
   * Remplacez **[chaîne de connexion d’IoT Hub]** par la `iot hub connection string` obtenue.

   > [!NOTE]
   > Vous n’avez pas besoin de `azure_storage_connection_string` dans cet article. Gardez-le tel quel.

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

## Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application sur votre carte Arduino en exécutant la commande suivante :

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

> [!NOTE]
> La tâche gulp par défaut exécute séquentiellement les tâches `install-tools` et `run`. Lorsque vous [avez déployé l’application de clignotement (blink)][deployed-the-blink-app], vous avez exécuté ces tâches séparément.

<a id="verify-that-the-sample-application-works" class="xliff"></a>

## Vérification du bon fonctionnement de l’exemple d’application
Vous devriez voir la LED embarquée GPIO #0 clignoter toutes les deux secondes. Chaque fois que la LED clignote, l’exemple d’application envoie un message à votre IoT Hub et vérifie que le message a été correctement envoyé. De plus, chaque message reçu par l’IoT Hub apparaît dans la fenêtre de console. L’exemple d’application se termine automatiquement après l’envoi de 20 messages.

![Exemple d’application avec des messages envoyés et reçus][sample-application-with-sent-and-received-messages]

<a id="summary" class="xliff"></a>

## Résumé
Vous avez déployé et exécuté le nouvel exemple d’application de clignotement sur votre carte Arduino pour l’envoi de messages appareil-à-cloud à votre IoT Hub. Vous surveillez désormais les messages à mesure qu’ils sont écrits dans le compte de stockage.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Lecture des messages conservés dans le stockage Azure][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[config-arduino-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/config-arduino.png
[deployed-the-blink-app]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_run_arduino.png
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
