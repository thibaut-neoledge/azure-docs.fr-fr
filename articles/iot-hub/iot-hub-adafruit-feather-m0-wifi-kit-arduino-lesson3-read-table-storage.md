---
title: "Connecter Arduino (C) à Azure IoT - Leçon 3 : Stockage de tables | Microsoft Docs"
description: "Surveillez les messages appareil-à-cloud alors qu’ils sont écrits dans votre stockage Table Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "données dans le cloud, collecte de données cloud, service cloud iot, données iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 386083e0-0dbb-48c0-9ac2-4f8fb4590772
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 7a49a15b00bf25c9416235f41da8cc86e54d0062
ms.contentlocale: fr-fr
ms.lasthandoff: 01/24/2017

---
<a id="read-messages-persisted-in-azure-storage" class="xliff"></a>

# Lecture des messages conservés dans le stockage Azure
<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre
Surveillez les messages appareil-cloud qui sont envoyés à partir de votre carte Adafruit Feather M0 WiFi Arduino à votre IoT Hub, à mesure que les messages sont écrits dans le Stockage de table Azure.

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
Dans cet article, vous allez apprendre à utiliser la tâche de lecture de message gulp pour lire les messages conservés dans votre stockage Table Azure.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin
Avant de commencer, vous devez avoir correctement suivi la section [Exécution de l’exemple d’application de clignotement Azure sur votre carte Arduino][run-blink-application].

<a id="read-new-messages-from-your-storage-account" class="xliff"></a>

## Lecture des nouveaux messages à partir de votre compte de stockage
Dans l’article précédent, vous avez exécuté un exemple d’application sur votre carte Arduino. L’exemple d’application a envoyé des messages à votre Azure IoT Hub. Les messages envoyés à votre IoT Hub sont stockés dans votre stockage Table Azure via l’application de fonction Azure. Pour lire les messages à partir de votre stockage Table Azure, vous avez besoin de la chaîne de connexion du Stockage Azure.

Pour lire les messages stockés dans votre stockage Table Azure, procédez comme suit :

1. Obtenez la chaîne de connexion en exécutant la commande suivante :

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   La première commande récupère le `storage name` utilisé dans la deuxième commande pour obtenir la chaîne de connexion. Si vous n’avez pas modifié la valeur, utilisez `iot-sample` en tant que valeur de `{resource group name}`.
2. Ouvrez le fichier de configuration `config-arduino.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-arduino.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-arduino.json
   ```
3. Remplacez `[Azure storage connection string]` par la chaîne de connexion que vous avez obtenue à l’étape 1.
4. Enregistrez le fichier `config-arduino.json`.
5. Envoyez de nouveau des messages et lisez-les à partir de votre stockage Table Azure en exécutant la commande suivante :

   ```bash
   gulp run --read-storage

   # You can monitor the serial port by running listen task:
   gulp listen

   # Or you can combine above two gulp tasks into one:
   gulp run --read-storage --listen
   ```

   La logique pour la lecture à partir du stockage Table Azure se trouve dans le fichier `azure-table.js`.

   ![exécutez gulp --lire-stockage][gulp-run]

<a id="summary" class="xliff"></a>

## Résumé
Vous avez correctement connecté votre carte Arduino à votre IoT Hub dans le cloud, et utilisé l’exemple d’application de clignotement pour envoyer des messages appareil-à-cloud. Vous avez également utilisé l’application de fonction Azure pour stocker des messages d’IoT Hub entrants votre stockage Table Azure. Vous pouvez désormais envoyer des messages cloud-à-appareil depuis votre IoT Hub vers votre carte Arduino.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Envoyer des messages Cloud vers appareil][send-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[run-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[gulp-run]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/gulp_read_message_arduino.png
[send-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
