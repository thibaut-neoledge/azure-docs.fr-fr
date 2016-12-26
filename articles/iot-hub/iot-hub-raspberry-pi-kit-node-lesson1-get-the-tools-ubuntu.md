---
title: Obtenir les outils (Ubuntu 16.04) | Microsoft Docs
description: "Téléchargez et installez les outils et logiciels nécessaires pour le premier exemple d’application pour Pi sur Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "développement iot, logiciel iot, logiciel internet des objets, installer git sur ubuntu, exécuter gulp, installer node js ubuntu"
ms.assetid: 4d5e45c0-1db9-4662-a039-99ba26333085
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: 31b5ac4e60eaafcc134ee702a4d47c4ea735df39


---
# <a name="get-the-tools-ubuntu-1604"></a>Obtenir les outils (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)


## <a name="what-you-will-do"></a>Procédure à suivre
Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour Raspberry Pi 3. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :

* Installation de Git et Node.js.
  * [Git](https://git-scm.com) est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cet article est stocké sur Git.
  * [Node.js](https://nodejs.org/en/) est un runtime JavaScript avec un écosystème de packages riche.
* Comment utiliser NPM pour installer des outils de développement Node.js supplémentaires.
  * La version minimale requise de Node.js est 4.5 LTS.
  * [NPM](https://www.npmjs.com) est l’un des gestionnaires de packages pour Node.js.

## <a name="what-do-you-need"></a>Ce dont vous avez besoin
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
Vous utilisez [gulp.js](http://gulpjs.com) pour automatiser le déploiement de l’exemple d’application sur Pi. Vous utilisez également la [device-discovery-cli](https://github.com/Azure/device-discovery-cli) (interface de ligne de commande de découverte d’appareils) pour récupérer les informations réseau concernant vos appareils IoT.

Installez `gulp` et `device-discovery-cli` en exécutant la commande suivante dans la fenêtre de terminal :

```bash
sudo npm install -g device-discovery-cli gulp
```

Si vous rencontrez des problèmes pour installer Node.js et ces outils de développement supplémentaires sur Ubuntu, pour trouver des solutions aux problèmes courants, voir le [guide de dépannage](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="install-visual-studio-code"></a>Installation de Visual Studio Code
[Téléchargez](https://code.visualstudio.com/docs/setup/linux) et installez Visual Studio Code. Visual Studio Code est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utiliserez cet éditeur ultérieurement dans ce didacticiel pour modifier l’exemple de code.

## <a name="summary"></a>Résumé
Vous avez installé les outils de développement et le logiciel nécessaire pour le premier exemple d’application. La tâche suivante consiste à créer, déployer et exécuter l’exemple d’application sur Pi.

## <a name="next-steps"></a>Étapes suivantes
[Création et déploiement de l’exemple d’application de clignotement](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)




<!--HONumber=Nov16_HO5-->


