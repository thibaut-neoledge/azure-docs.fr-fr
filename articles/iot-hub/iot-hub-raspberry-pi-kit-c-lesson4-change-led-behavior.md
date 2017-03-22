---
title: "Connecter Raspberry Pi (C) à Azure IoT - Leçon 4 : Modifier une application | Microsoft Docs"
description: "Personnalisez les messages pour modifier le comportement activé/désactivé de la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "contrôler la led avec raspberry pi, contrôle de la led raspberry pi, raspberry pi contrôle de la led"
ms.assetid: 0201b8ed-d5e6-4445-9a4d-1305003d1eff
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: c1c999e40ecc92a1018067822be66f14b61a8c36
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modification du comportement activé/désactivé de la LED
## <a name="what-you-will-do"></a>Procédure à suivre
Personnalisez les messages pour modifier le comportement activé/désactivé de la LED. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu
Utiliser des fonctions supplémentaires de Node.js pour modifier le comportement activé/désactivé de la LED.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Vous devez avoir correctement suivi la section [Exécution d’un exemple d’application sur Raspberry Pi pour recevoir des messages cloud-à-appareil](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md).

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
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![Fichier main.c avec fonctions ajoutées](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_c.png)
3. Ajoutez les conditions suivantes avant la condition par défaut dans le bloc `if` de la fonction `receiveMessageCallback` :

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

   ![Fichier Gulpfile.js avec fonction ajoutée](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile_c.png)
5. Dans la fonction `sendMessage`, remplacez la ligne `var message = buildMessage(sentMessageCount);` par la nouvelle ligne illustrée dans l’extrait de code suivant :

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. Enregistrez toutes les modifications.

### <a name="deploy-and-run-the-sample-application"></a>Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application sur Pi en exécutant la commande suivante :

```bash
gulp deploy && gulp run
```

La LED doit s’allumer pendant deux secondes, puis s’éteindre pendant deux secondes. Le dernier message de « stop » arrête l’exécution de l’exemple d’application.

![Exemple d’application messages d’activation et de désactivation](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off_c.png)

Félicitations ! Vous avez correctement personnalisé les messages qui sont envoyés à Pi à partir de votre IoT Hub.

### <a name="summary"></a>Résumé
Cette section facultative montre comment personnaliser les messages de sorte que l’exemple d’application puisse contrôler le comportement activé/désactivé de la LED d’une autre manière.

