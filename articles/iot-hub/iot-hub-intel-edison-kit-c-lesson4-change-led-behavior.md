---
title: "Connecter Intel Edison (C) à Azure IoT - Leçon 4 : Faire clignoter la LED | Microsoft Docs"
description: "Personnalisez les messages pour modifier le comportement activé/désactivé de la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "contrôle de la led avec arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 9826c55a-0e24-4296-ae54-29b7fe66436a
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 278bdf74e2fa8f7074bb8f5ed8eae2d47402b299
ms.contentlocale: fr-fr
ms.lasthandoff: 01/25/2017

---
<a id="change-the-on-and-off-behavior-of-the-led" class="xliff"></a>

# Modification du comportement activé/désactivé de la LED
<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre
Personnalisez les messages pour modifier le comportement activé/désactivé de la LED. Si vous rencontrez des problèmes, recherchez des solutions sur la [page de résolution des problèmes][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
Utiliser des fonctions supplémentaires pour modifier le comportement activé/désactivé de la LED.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin
Vous devez avoir correctement suivi la section [Exécution d’un exemple d’application sur Intel Edison pour recevoir des messages cloud-à-appareil][receive-cloud-to-device-messages].

<a id="add-functions-to-mainc-and-gulpfilejs" class="xliff"></a>

## Ajouter des fonctions à main.c et gulpfile.js
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

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

### Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application sur Edison en exécutant la commande suivante :

```bash
gulp deploy && gulp run
```

La LED doit s’allumer pendant deux secondes, puis s’éteindre pendant deux secondes. Le dernier message de « stop » arrête l’exécution de l’exemple d’application.

![activer et désactiver][on-and-off]

Félicitations ! Vous avez correctement personnalisé les messages qui sont envoyés à Edison à partir de votre IoT Hub.

<a id="summary" class="xliff"></a>

### Résumé
Cette section facultative montre comment personnaliser les messages de sorte que l’exemple d’application puisse contrôler le comportement activé/désactivé de la LED d’une autre manière.

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-c-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_c.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_c.png

