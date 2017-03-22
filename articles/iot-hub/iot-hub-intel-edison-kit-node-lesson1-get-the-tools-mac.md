---
title: "Connecter Intel Edison (Node) à Azure IoT - Leçon 1 : Obtenir des outils (macOS) | Microsoft Docs"
description: "Téléchargez et installez les outils et logiciels nécessaires pour le premier exemple d’application pour Edison sur macOS."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "outils de développement arduino, développement iot, logiciel iot, logiciel internet des objets, installer git sur mac, installer node js mac"
ms.assetid: fb6742be-2825-4524-89f7-8ccb7e7f1de1
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 7d8c4ef5cf055930178408ee9751058392a2f894
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-macos-1010"></a>Obtenir les outils (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Procédure à suivre
Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour Intel Edison. Si vous rencontrez des problèmes, recherchez des solutions sur la [page de résolution des problèmes][troubleshooting].

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :

* Installation de Git et Node.js.
  * [Git](https://git-scm.com) est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cet article est stocké sur Git.
  * [Node.js](https://nodejs.org/en/) est un runtime JavaScript avec un écosystème de packages riche.
* Comment utiliser NPM pour installer des outils de développement Node.js supplémentaires.
  * La version minimale requise de Node.js est 4.5 LTS.
  * [NPM](https://www.npmjs.com) est l’un des gestionnaires de packages pour Node.js.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Pour effectuer cette opération, vous avez besoin des éléments suivants :
* Une connexion Internet pour télécharger les outils de développement et le logiciel.
* Un Mac exécutant macOS Yosemite (10.10) ou version ultérieure.

## <a name="install-git-and-nodejs"></a>Installation de Git et Node.js
Pour installer Git et Node.js, servez-vous de l’utilitaire de gestion de package [Homebrew](http://brew.sh) en procédant comme suit :

1. Installer Homebrew. Si vous avez déjà installé Homebrew, passez à l’étape 2.

   1. Appuyez sur `Cmd + Space` puis entrez `Terminal` pour ouvrir une fenêtre de terminal.
   2. Exécutez la commande suivante :

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```
2. Installez de Git et Node.js en exécutant la commande suivante :

   ```bash
   brew install node git
   ```

## <a name="install-additional-nodejs-development-tools"></a>Installation des outils de développement Node.js supplémentaires
Utilisez [gulp.js](http://gulpjs.com) pour automatiser le déploiement de l’exemple d’application sur votre Edison.

Installez `gulp` en exécutant la commande suivante dans le terminal :

```bash
sudo npm install -g gulp
```

Si vous rencontrez des problèmes pour installer Node.js et ces outils de développement supplémentaires sur macOS, consultez le [guide de dépannage][troubleshooting] pour trouver des solutions aux problèmes courants.

## <a name="install-visual-studio-code"></a>Installation de Visual Studio Code
[Téléchargez](https://code.visualstudio.com/docs/setup/osx) et installez Visual Studio Code. Visual Studio Code est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utiliserez cet éditeur ultérieurement dans ce didacticiel pour modifier l’exemple de code.

## <a name="summary"></a>Résumé
Vous avez installé les outils de développement et le logiciel nécessaire pour le premier exemple d’application. La tâche suivante consiste à créer, déployer et exécuter l’exemple d’application sur Edison.

## <a name="next-steps"></a>Étapes suivantes
[Créer et déployer l’application Blink][create-and-deploy-the-blink-application]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-node-lesson1-deploy-blink-app.md
[windows]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-mac.md

