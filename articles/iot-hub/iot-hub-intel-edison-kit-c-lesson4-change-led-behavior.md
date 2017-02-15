---
title: "Modifier le comportement du clignotement de la LED avec des messages à partir d’Azure IoT Hub | Microsoft Docs"
description: "Personnalisez les messages pour modifier le comportement activé/désactivé de la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "contrôle de la led avec arduino"
ms.assetid: 9826c55a-0e24-4296-ae54-29b7fe66436a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 6d1c09c8007c373ab3c50a99efb1b3f27b46d90a
ms.openlocfilehash: 858e68a3794470f51ee505710e16496109c1aa0b


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modification du comportement activé/désactivé de la LED
## <a name="what-you-will-do"></a>Procédure à suivre
Personnalisez les messages pour modifier le comportement activé/désactivé de la LED. Si vous rencontrez des problèmes, recherchez des solutions sur la [page de résolution des problèmes][troubleshooting].

## <a name="what-you-will-learn"></a>Contenu
Utiliser des fonctions supplémentaires pour modifier le comportement activé/désactivé de la LED.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Vous devez avoir correctement suivi la section [Exécution d’un exemple d’application sur Intel Edison pour recevoir des messages cloud-à-appareil][receive-cloud-to-device-messages].

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>Ajouter des fonctions à main.c et gulpfile.js
1. Ouvrez l’exemple d’application dans Visual Studio Code en exécutant les commandes suivantes :

   ```bash
   cd Lesson4
   code .
   ```
2. Ouvrez le fichier `main.c` et ajoutez les fonctions suivantes après la fonction blinkLED() :

   ```c
   static void turnOnLED()
   {
     mraa_gpio_write(context, 1);
   }

   static void turnOffLED()
   {
     mraa_gpio_write(context, 0);
   }
   ```

   ![Fichier main.c avec fonctions ajoutées](media/iot-hub-intel-edison-lessons/lesson4/updated_app_c.png)

3. Ajoutez les conditions suivantes avant d’effectuer le bloc `else if` de la fonction `receiveMessageCallback` :

   ```c
   else if (0 == strcmp((const char*)value, "\"on\""))
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\""))
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
   }
   ```

   ![Fichier Gulpfile.js avec fonction ajoutée][gulpfile]
5. Dans la fonction `sendMessage`, remplacez la ligne `var message = buildMessage(sentMessageCount);` par la nouvelle ligne illustrée dans l’extrait de code suivant :

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Enregistrez toutes les modifications.

### <a name="deploy-and-run-the-sample-application"></a>Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application sur Edison en exécutant la commande suivante :

```bash
gulp deploy && gulp run
```

La LED doit s’allumer pendant deux secondes, puis s’éteindre pendant deux secondes. Le dernier message de « stop » arrête l’exécution de l’exemple d’application.

![activer et désactiver][on-and-off]

Félicitations ! Vous avez correctement personnalisé les messages qui sont envoyés à Edison à partir de votre IoT Hub.

### <a name="summary"></a>Résumé
Cette section facultative montre comment personnaliser les messages de sorte que l’exemple d’application puisse contrôler le comportement activé/désactivé de la LED d’une autre manière.

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_c.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_c.png



<!--HONumber=Dec16_HO2-->


