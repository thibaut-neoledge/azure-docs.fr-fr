---
title: "Connecter Arduino (C) à Azure IoT - Leçon 4 : Modifier une application | Microsoft Docs"
description: "Personnalisez les messages pour modifier le comportement activé/désactivé de la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "contrôle de la LED avec arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: d7a25430-450e-43c4-a3ed-1eed995b8b7e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3585dfbac8816140c0a62931920aff1a6bf7d540
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modification du comportement activé/désactivé de la LED
## <a name="what-you-will-do"></a>Procédure à suivre
Personnalisez les messages pour modifier le comportement activé/désactivé de la LED. Si vous rencontrez des problèmes, recherchez une solution sur la [page de résolution des problèmes](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md) pour la carte Adafruit Feather M0 WiFi Arduino.

## <a name="what-you-will-learn"></a>Contenu
Utiliser des fonctions supplémentaires Arduino pour modifier le comportement activé/désactivé de la LED.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Vous devez avoir correctement suivi la section [Exécution d’un exemple d’application sur votre carte Arduino pour recevoir des messages cloud-à-appareil][receive-cloud-to-device-messages].

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>Ajouter des fonctions à main.c et gulpfile.js
1. Ouvrez l’exemple d’application dans Visual Studio Code en exécutant les commandes suivantes :

   ```bash
   cd Lesson4
   code .
   ```
2. Ouvrez le fichier `app.ino` et ajoutez les fonctions suivantes après la fonction blinkLED() :

   ```arduino
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![Fichier app.ino avec fonctions ajoutées][app-ino-file]
3. Ajoutez les conditions suivantes avant d’effectuer le bloc `else if` de la fonction `receiveMessageCallback` :

   ```arduino
   else if (strcmp((const char*)value, "\"on\"") == 0)
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\"") == 0)
   {
       turnOffLED();
   }
   ```

   Vous avez maintenant configuré l’exemple d’application pour répondre à davantage d’instructions envoyées via des messages. L’instruction « on » active la LED et l’instruction « off » la désactive.
4. Ouvrez le fichier gulpfile.js, puis ajoutez une nouvelle fonction devant la fonction `sendMessage` :

   ```javascript
   var buildCustomMessage = function (messageId) {
     if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
       return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
     } else if (messageId < MAX_MESSAGE_COUNT) {
       return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
     } else {
       return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
     }
   };
   ```

   ![Fichier Gulpfile.js avec fonction ajoutée][gulp-file-js]
5. Dans la fonction `sendMessage`, remplacez la ligne `var message = buildMessage(sentMessageCount);` par la nouvelle ligne illustrée dans l’extrait de code suivant :

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Enregistrez toutes les modifications.

### <a name="deploy-and-run-the-sample-application"></a>Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application sur votre carte Arduino en exécutant la commande suivante :

```bash
gulp run
# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

La LED doit s’allumer pendant deux secondes, puis s’éteindre pendant deux secondes. Le dernier message de « stop » arrête l’exécution de l’exemple d’application.

![activer et désactiver][on-and-off]

Félicitations ! Vous avez correctement personnalisé les messages qui sont envoyés à votre carte Arduino à partir de votre IoT Hub.

### <a name="summary"></a>Résumé
Cette section facultative montre comment personnaliser les messages de sorte que l’exemple d’application puisse contrôler le comportement activé/désactivé de la LED d’une autre manière.

<!-- Images and links -->

[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[app-ino-file]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_app_ino.png
[gulp-file-js]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/updated_gulpfile_js.png
[on-and-off]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson4/gulp_on_and_off_arduino.png
