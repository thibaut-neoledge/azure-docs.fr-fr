---
title: "Connecter Raspberry Pi (C) à Azure IoT - Leçon 1 : Obtenir des outils (Windows) | Microsoft Docs"
description: "Téléchargez et installez les outils et logiciels nécessaires pour le premier exemple d’application pour Pi sur Windows 7 et versions ultérieures."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "développement iot, logiciel iot, logiciel internet des objets, installer git sur windows, installer node js sur windows, installer npm sur windows"
ms.assetid: bd765ddd-65b7-4241-a391-dc77cb3af1c0
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: d53efc7d25714427e25d4f88279f3b0d4f61c150
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-windows-7-or-later"></a>Obtenir les outils (Windows 7 ou version ultérieure)

> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Procédure à suivre
Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour Raspberry Pi 3. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

> [!NOTE]
> Le langage de programmation de la logique principale est C. Cependant, les outils Node.js sont utilisés dans cette leçon pour découvrir des appareils et créer et déployer des exemples d’applications.

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :

* Installation de Git et Node.js.
  * [Git](https://git-scm.com) est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cet article est stocké sur Git.
  * [Node.js](https://nodejs.org/en/) est un runtime JavaScript avec un écosystème de packages riche.
* Comment utiliser NPM pour installer des outils de développement Node.js supplémentaires.
  * La version minimum requise de Node.js est 4.5 LTS.
  * [NPM](https://www.npmjs.com) est l’un des gestionnaires de packages pour Node.js.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Pour effectuer cette opération, vous avez besoin des éléments suivants :

* Une connexion Internet pour télécharger les outils de développement et le logiciel.
* Un ordinateur fonctionnant sous Windows.

## <a name="install-git-and-nodejs"></a>Installation de Git et Node.js

Cliquez sur les liens ci-dessous pour télécharger et installer Git et Node.js LTS pour Windows.

* [Téléchargement de Git pour Windows](https://git-scm.com/download/win/)
* [Téléchargement de Node.js LTS pour Windows](https://nodejs.org/en/)

## <a name="install-additional-nodejs-development-tools"></a>Installation des outils de développement Node.js supplémentaires

Utilisez [gulp.js](http://gulpjs.com) pour automatiser le déploiement de l’exemple d’application sur Pi. Utilisez [device-discovery-cli](https://github.com/Azure/device-discovery-cli) (interface de ligne de commande de découverte d’appareils) pour récupérer les informations réseau concernant vos appareils IoT.

Lancez une invite de commandes en tant qu’administrateur. Installez `gulp` et `device-discovery-cli` en exécutant la commande suivante :

```bash
npm install -g device-discovery-cli gulp
```

Si vous rencontrez des problèmes pour installer Node.js et ces outils de développement Node.js supplémentaires sur votre ordinateur, consultez le [guide de dépannage](iot-hub-raspberry-pi-kit-c-troubleshooting.md) des problèmes courants.

## <a name="install-visual-studio-code"></a>Installation de Visual Studio Code

[Téléchargez](https://code.visualstudio.com/docs/setup/windows) et installez Visual Studio Code. Visual Studio Code est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utiliserez cet éditeur ultérieurement dans ce didacticiel pour modifier l’exemple de code.

## <a name="summary"></a>Résumé

Vous avez installé les outils de développement et le logiciel nécessaire pour le premier exemple d’application. La tâche suivante consiste à créer, déployer et exécuter l’exemple d’application sur Pi.

## <a name="next-steps"></a>Étapes suivantes

[Créer et déployer l’application blink](iot-hub-raspberry-pi-kit-c-lesson1-deploy-blink-app.md)

