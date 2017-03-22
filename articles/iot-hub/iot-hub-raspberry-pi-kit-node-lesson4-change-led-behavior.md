---
title: "Connecter Raspberry Pi (Node) à Azure IoT - Leçon 4 : Modifier une application | Microsoft Docs"
description: "Personnalisez les messages pour modifier le comportement activé/désactivé de la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "contrôler la led avec raspberry pi, contrôle de la led raspberry pi, raspberry pi contrôle de la led"
ms.assetid: 3b42a4ad-0197-42f6-8ca9-04c883e879e8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 53c9408bedc6c61cdb0e755b46b3090e8723e271
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>Modification du comportement activé/désactivé de la LED
## <a name="what-you-will-do"></a>Procédure à suivre
Personnalisez les messages pour modifier le comportement activé/désactivé de la LED. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu
Utiliser des fonctions supplémentaires de Node.js pour modifier le comportement activé/désactivé de la LED.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Vous devez avoir correctement suivi la section [Exécution d’un exemple d’application sur Raspberry Pi pour recevoir des messages cloud-à-appareil](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

## <a name="add-nodejs-functions"></a>Ajout de fonctions Node.js
1. Ouvrez l’exemple d’application dans Visual Studio Code en exécutant les commandes suivantes :
   
   ```bash
   cd Lesson4
   code .
   ```
2. Ouvrez le fichier `app.js`, puis ajoutez les fonctions suivantes à la fin :
   
   ```javascript
   function turnOnLED() {
     wpi.digitalWrite(CONFIG_PIN, 1);
   }
   
   function turnOffLED() {
     wpi.digitalWrite(CONFIG_PIN, 0);
   }
   ```
   
   ![Fichier App.js avec fonctions ajoutées](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)
3. Ajouter les conditions suivantes avant la condition par défaut dans le bloc switch-case de la fonction `receiveMessageCallback` :
   
   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
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
   
   ![Fichier Gulpfile.js avec fonction ajoutée](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)
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

![Exemple d’application messages d’activation et de désactivation](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

Félicitations ! Vous avez correctement personnalisé les messages qui sont envoyés à Pi à partir de votre IoT Hub.

### <a name="summary"></a>Résumé
Cette section facultative montre comment personnaliser les messages de sorte que l’exemple d’application puisse contrôler le comportement activé/désactivé de la LED d’une autre manière.


