---
title: "Connecter Intel Edison (Node) à Azure IoT - Leçon 1 : Déployer une application | Microsoft Docs"
description: "Clonez l’exemple d’application C à partir de GitHub et exécutez gulp pour déployer cette application sur votre carte Intel Edison. Avec cet exemple d’application, la LED connectée à la carte clignote toutes les deux secondes."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: projets de led arduino, clignotement de led arduino, code de clignotement de led arduino, programme de clignotement arduino, exemple de clignotement arduino
ms.assetid: ed2c21d0-c72c-4ac2-9e70-347e9a0711c0
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 182e63d40986b61a12885799b1b33bb570437a3c


---
# <a name="create-and-deploy-the-blink-application"></a>Créer et déployer l’application blink
## <a name="what-you-will-do"></a>Procédure à suivre
Clonez l’exemple d’application C à partir de GitHub et utilisez l’outil gulp pour le déployer sur Intel Edison. Avec cet exemple d’application, la LED connectée à la carte clignote toutes les deux secondes. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

## <a name="what-you-will-learn"></a>Contenu
* Déploiement et exécution de l’exemple d’application sur Edison.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Vous devez avoir terminé les opérations suivantes :

* [Configuration de votre appareil][configure-your-device]
* [Obtenir les outils][get-the-tools]

## <a name="open-the-sample-application"></a>Ouvrir l’exemple d’application
Procédez comme suit pour ouvrir l’exemple d’application :

1. Clonez l’exemple de référentiel à partir de GitHub en exécutant la commande suivante :

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-edison-getting-started.git
   ```
2. Ouvrez l’exemple d’application dans Visual Studio Code en exécutant les commandes suivantes :

   ```bash
   cd iot-hub-node-edison-getting-started
   cd Lesson1
   code .
   ```

   ![Structure du référentiel][repo-structure]

Le fichier dans le sous-dossier `app` est le fichier source clé qui contient le code pour contrôler la LED.

### <a name="install-application-dependencies"></a>Installation des dépendances de l’application
Installez les bibliothèques et d’autres modules dont vous avez besoin pour l’exemple d’application en exécutant la commande suivante :

```bash
npm install
```

## <a name="configure-the-device-connection"></a>Configuration de la connexion de l’appareil
Procédez comme suit pour configurer la connexion de l’appareil :

1. Générez le fichier de configuration de votre appareil en exécutant la commande suivante :

   ```bash
   gulp init
   ```

   Le fichier de configuration `config-edison.json` contient les informations d’identification utilisateur que vous utilisez pour vous connecter à Edison. Pour éviter la fuite d’informations d’identification de l’utilisateur, le fichier de configuration est généré dans le sous-dossier `.iot-hub-getting-started` du dossier de base de votre ordinateur.

2. Ouvrez le fichier de configuration de votre appareil dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

3. Remplacez les espaces réservés `[device hostname or IP address]` et `[device password]` par l’adresse IP et le mot de passe de la leçon précédente.

   ![Config.json](media/iot-hub-intel-edison-lessons/lesson1/vscode-config-mac.png)

Félicitations ! Vous avez créé le premier exemple d’application pour Edison.

## <a name="deploy-and-run-the-sample-application"></a>Déploiement et exécution de l’exemple d’application

### <a name="deploy-and-run-the-sample-app"></a>Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application en exécutant la commande suivante :

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>Vérification du bon fonctionnement de l’application
L’exemple d’application se termine automatiquement une fois que la LED a clignoté 20 fois. Si vous ne voyez pas la LED clignoter, consultez le [guide de dépannage][troubleshooting] des problèmes courants.

![LED clignotante][led-blinking]

## <a name="summary"></a>Résumé
Vous avez installé les outils nécessaires pour travailler avec Edison et déployé un exemple d’application sur Edison pour faire clignoter la LED. Vous pouvez maintenant créer, déployer et exécuter un autre exemple d’application qui connecte Edison à Azure IoT Hub pour envoyer et recevoir des messages.

## <a name="next-steps"></a>Étapes suivantes
[Obtenir les outils Azure][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md



<!--HONumber=Jan17_HO4-->


