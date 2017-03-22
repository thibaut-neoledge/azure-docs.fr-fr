---
featureFlags:
- usabilla
title: "Connecter Raspberry Pi (Node) à Azure IoT - Leçon 1 : Déployer une application | Microsoft Docs"
description: "Clonez l’exemple d’application Node.js à partir de GitHub et utilisez gulp pour déployer cette application sur votre carte Raspberry Pi 3. Avec cet exemple d’application, la LED connectée à la carte clignote toutes les deux secondes."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: raspberry pi led clignotante, led clignotante avec raspberry pi
ms.assetid: a5a03a57-fe86-416f-90ff-6eca17775842
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 90ceb432bd9506dc40e340af21a3cae4e21a20b0
ms.lasthandoff: 01/24/2017


---
# <a name="create-and-deploy-the-blink-application"></a>Créer et déployer l’application blink
## <a name="what-you-will-do"></a>Procédure à suivre
Clonez l’exemple d’application Node.js à partir de GitHub et utilisez l’outil gulp pour le déployer sur votre Raspberry Pi 3. Avec cet exemple d’application, la LED connectée à la carte clignote toutes les deux secondes. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :

* Utilisation de l’outil `device-discover-cli` pour récupérer des informations de mise en réseau sur Pi.
* Déploiement et exécution de l’exemple d’application sur Pi.
* Déploiement et débogage des applications en cours d’exécution à distance sur Pi.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Vous devez avoir terminé les opérations suivantes :

* [Configuration de votre appareil](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)
* [Obtention des outils](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

## <a name="obtain-the-ip-address-and-host-name-of-pi"></a>Obtention de l’adresse IP et du nom d’hôte de Pi
Ouvrez une invite de commandes dans Windows ou une fenêtre de terminal dans Mac OS ou Ubuntu, puis exécutez la commande suivante :

```bash
devdisco list --eth
```

Le résultat doit ressembler à ce qui suit :

![Découverte de l’appareil](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Notez l’`IP address` et le `hostname` de Pi. Ces informations vous seront nécessaires plus loin dans cet article.

> [!NOTE]
> Assurez-vous que Pi est connecté au même réseau que votre ordinateur. Par exemple, si votre ordinateur est connecté à un réseau sans fil alors que Pi est connecté à un réseau câblé, il se peut que vous ne voyiez pas l’adresse IP dans la sortie devdisco.

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application
Procédez comme suit pour ouvrir l’exemple de code :

1. Clonez l’exemple de référentiel à partir de GitHub en exécutant la commande suivante :
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started.git
   ```
2. Ouvrez l’exemple d’application dans Visual Studio Code en exécutant les commandes suivantes :
   
   ```bash
   cd iot-hub-node-raspberrypi-getting-started
   cd Lesson1
   code .
   ```

![Structure du référentiel](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-mac.png)

Le fichier `app.js` dans le sous-dossier `app` est le fichier source clé qui contient le code pour contrôler la LED.

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
   
   Le fichier de configuration `config-raspberrypi.json` contient les informations d’identification utilisateur que vous utilisez pour vous connecter à Pi. Pour éviter la fuite d’informations d’identification de l’utilisateur, le fichier de configuration est généré dans le sous-dossier `.iot-hub-getting-started` du dossier de base de votre ordinateur.

2. Ouvrez le fichier de configuration de votre appareil dans Visual Studio Code en exécutant la commande suivante :
   
   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
   
3. Remplacez l’espace réservé `[device hostname or IP address]` par l’adresse IP ou le nom d’hôte que vous avez obtenu précédemment dans « Obtention de l’adresse IP et du nom d’hôte de Pi ».
   
   ![Config.json](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

> [!NOTE]
> Lors de la connexion à Raspberry Pi, vous pouvez utiliser une clé SSH au lieu du nom d’utilisateur et du mot de passe. Pour cela, vous devez générer la clé à l’aide de **ssh-keygen** et **ssh-copy-id pi@\<adresse de l’appareil\>**.
>
> Sous Windows, ces commandes sont disponibles dans **Git Bash**.
>
> Sous Mac OS, vous devez exécuter **brew install ssh-copy-id**.
>
> Après avoir téléchargé la clé de Raspberry Pi, remplacez **device_password** par la propriété **device_key_path** dans **config-raspberrypi.json**.
>
> Les lignes mises à jour doivent se présenter comme suit :
> ```javascript
> "device_user_name": "pi",
> "device_key_path": "id_rsa",
> ```

Félicitations ! Vous avez créé le premier exemple d’application pour Pi.

## <a name="deploy-and-run-the-sample-application"></a>Déploiement et exécution de l’exemple d’application
### <a name="install-nodejs-and-npm-on-pi"></a>Installez Node.js et NPM sur Pi
Installez Node.js et NPM sur Pi en exécutant la commande suivante :

```bash
gulp install-tools
```

La première exécution de cette tâche peut prendre jusqu’à 10 minutes.

### <a name="deploy-and-run-the-sample-app"></a>Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application en exécutant la commande suivante :

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>Vérification du bon fonctionnement de l’application
Vous devez maintenant voir la LED sur Pi clignoter toutes les deux secondes.  Si vous ne voyez pas la LED clignoter, consultez le [guide de dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md) des problèmes courants.
![LED clignotante](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

## <a name="summary"></a>Résumé
Vous avez installé les outils nécessaires pour travailler avec Pi et déployé un exemple d’application sur Pi pour faire clignoter la LED. Vous pouvez maintenant créer, déployer et exécuter un autre exemple d’application qui connecte Pi à Azure IoT Hub pour envoyer et recevoir des messages.

## <a name="next-steps"></a>Étapes suivantes
[Obtenir les outils Azure](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)


