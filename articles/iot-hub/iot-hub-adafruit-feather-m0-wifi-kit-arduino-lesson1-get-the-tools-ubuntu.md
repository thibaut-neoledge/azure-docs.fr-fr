---
title: "Connecter Arduino à Azure IoT - Leçon 1 : Obtenir des outils (Ubuntu) | Microsoft Docs"
description: "Téléchargez et installez les outils et logiciels nécessaires pour le premier exemple d’application pour Adafruit Feather M0 WiFi sur Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "outils de développement arduino, développement iot, logiciel iot, logiciel internet des objets, installer git sur ubuntu, installer node js ubuntu"
ms.assetid: 7572f191-420d-41f0-923b-7ea86c0bfa73
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: d33fc9b04f69b161e92a266cd3604488e35e74af
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-ubuntu-1604"></a>Obtenir les outils (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Procédure à suivre

Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour votre carte Adafruit Feather M0 WiFi Arduino. 

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

> [!NOTE]
> Le langage de programmation de la logique principale est Arduino. Cependant, les outils Node.js sont utilisés dans cette leçon pour créer et déployer des exemples d’applications.

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :

* Comment installer Git et Node.js
  * [Git](https://git-scm.com) est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cet article est stocké sur Git.
  * [Node.js](https://nodejs.org/en/) est un runtime JavaScript avec un écosystème de packages riche.
* Comment utiliser NPM pour installer des outils de développement Node.js supplémentaires.
  * La version minimale requise de Node.js est 4.5 LTS.
  * [NPM](https://www.npmjs.com) est l’un des gestionnaires de packages pour Node.js.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Pour effectuer cette opération, vous avez besoin des éléments suivants :
* Une connexion Internet pour télécharger les outils de développement et le logiciel.
* Un ordinateur exécutant Ubuntu 16.04 ou version ultérieure.

## <a name="install-git-nodejs-and-npm"></a>Installation de Git, Node.js et NPM
Utilisez le raccourci clavier `Ctrl + Alt + T` pour ouvrir une fenêtre de terminal, puis exécutez les commandes suivantes :

```bash
sudo apt-get update
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install git
```

## <a name="install-additional-nodejs-development-tools"></a>Installation des outils de développement Node.js supplémentaires
Utilisez [gulp.js](http://gulpjs.com) pour automatiser le déploiement de l’exemple d’application sur votre carte Arduino.

Installez `gulp`, `device-discovery-cli` en exécutant la commande suivante dans le terminal :

```bash
sudo npm install -g gulp device-discovery-cli
```

Si vous rencontrez des problèmes pour installer Node.js et ces outils de développement supplémentaires sur Ubuntu, consultez le [guide de dépannage][troubleshooting] pour trouver des solutions aux problèmes courants.

## <a name="install-visual-studio-code"></a>Installation de Visual Studio Code
[Téléchargez](https://code.visualstudio.com/docs/setup/linux) et installez Visual Studio Code. Visual Studio Code est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utiliserez cet éditeur ultérieurement dans ce didacticiel pour modifier l’exemple de code.

## <a name="summary"></a>Résumé
Vous avez installé les outils de développement et le logiciel nécessaire pour le premier exemple d’application. La tâche suivante consiste à créer, déployer et exécuter l’exemple d’application sur votre carte Arduino.

## <a name="next-steps"></a>Étapes suivantes
[Création et déploiement de l’exemple d’application de clignotement][create-and-deploy-the-blink-sample-application]

<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-mac.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[create-and-deploy-the-blink-sample-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
