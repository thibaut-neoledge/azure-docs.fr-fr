---
title: Obtenir les outils (Ubuntu 16.04) | Microsoft Docs
description: "Téléchargez et installez les outils et logiciels nécessaires pour le premier exemple d’application pour Pi sur Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "développement iot, logiciel iot, logiciel internet des objets, installer git sur ubuntu, exécuter gulp, installer node js ubuntu"
ms.assetid: 32cfea00-c254-4cef-8f6f-bbf807eca6b6
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 155e5d6280d86b06b1718fc3032c2c224539183d
ms.openlocfilehash: 011f44585676684ca173a6f83b79f3973f877743


---
# <a name="get-the-tools-ubuntu-1604"></a>Obtenir les outils (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Procédure à suivre
Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour votre Raspberry Pi 3. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

> [!NOTE]
> Le langage de programmation de la logique principale est C. Cependant, les outils Node.js sont utilisés dans cette leçon pour découvrir des appareils et créer et déployer des exemples d’applications.

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
Utilisez [gulp.js](http://gulpjs.com) pour automatiser le déploiement de l’exemple d’application sur Pi. Utilisez [device-discovery-cli](https://github.com/Azure/device-discovery-cli) (interface de ligne de commande de découverte d’appareils) pour récupérer les informations réseau concernant vos appareils IoT.

Installez `gulp` et `device-discovery-cli` en exécutant la commande suivante dans la fenêtre de terminal :

```bash
sudo npm install -g device-discovery-cli gulp
```

Si vous rencontrez des problèmes pour installer Node.js et ces outils de développement supplémentaires sur Ubuntu, pour trouver des solutions aux problèmes courants, voir le [guide de dépannage](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="install-visual-studio-code"></a>Installation de Visual Studio Code
[Téléchargez](https://code.visualstudio.com/docs/setup/linux) et installez Visual Studio Code. Visual Studio Code est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utiliserez cet éditeur ultérieurement dans ce didacticiel pour modifier l’exemple de code.

## <a name="summary"></a>Résumé
Vous avez installé les outils de développement et le logiciel nécessaire pour le premier exemple d’application. La tâche suivante consiste à créer, déployer et exécuter l’exemple d’application sur Pi.

## <a name="next-steps"></a>Étapes suivantes
[Créer et déployer l’application blink](iot-hub-raspberry-pi-kit-c-lesson1-deploy-blink-app.md)




<!--HONumber=Dec16_HO1-->


