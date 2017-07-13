---
title: "Connecter Intel Edison (Node) à Azure IoT - Leçon 1 : Obtenir des outils (Ubuntu) | Microsoft Docs"
description: "Téléchargez et installez les outils et logiciels nécessaires pour le premier exemple d’application pour Edison sur Ubuntu."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "outils de développement arduino, développement iot, logiciel iot, logiciel internet des objets, installer git sur ubuntu, installer node js ubuntu"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: 9ab5b161-7ec5-41a6-9c5f-4456e4882752
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: c56699b81d83119ca1822c2efa6b2380b3619290
ms.contentlocale: fr-fr
ms.lasthandoff: 01/25/2017

---
<a id="get-the-tools-ubuntu-1604" class="xliff"></a>

# Obtenir les outils (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre
Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour Intel Edison. Si vous rencontrez des problèmes, recherchez des solutions sur la [page de résolution des problèmes][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
Cet article portera sur les éléments suivants :

* Comment installer Git et Node.js
  * [Git](https://git-scm.com) est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cet article est stocké sur Git.
  * [Node.js](https://nodejs.org/en/) est un runtime JavaScript avec un écosystème de packages riche.
* Comment utiliser NPM pour installer des outils de développement Node.js supplémentaires.
  * La version minimale requise de Node.js est 4.5 LTS.
  * [NPM](https://www.npmjs.com) est l’un des gestionnaires de packages pour Node.js.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin
Pour effectuer cette opération, vous avez besoin des éléments suivants :
* Une connexion Internet pour télécharger les outils de développement et le logiciel.
* Un ordinateur exécutant Ubuntu 16.04 ou version ultérieure.

<a id="install-git-nodejs-and-npm" class="xliff"></a>

## Installation de Git, Node.js et NPM
Utilisez le raccourci clavier `Ctrl + Alt + T` pour ouvrir une fenêtre de terminal, puis exécutez les commandes suivantes :

```bash
sudo apt-get update
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install git
```

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## Installation des outils de développement Node.js supplémentaires
Utilisez [gulp.js](http://gulpjs.com) pour automatiser le déploiement de l’exemple d’application sur Edison.

Installez `gulp` en exécutant la commande suivante dans le terminal :

```bash
sudo npm install -g gulp
```

Si vous rencontrez des problèmes pour installer Node.js et ces outils de développement supplémentaires sur Ubuntu, consultez le [guide de dépannage][troubleshooting] pour trouver des solutions aux problèmes courants.

<a id="install-visual-studio-code" class="xliff"></a>

## Installation de Visual Studio Code
[Téléchargez](https://code.visualstudio.com/docs/setup/linux) et installez Visual Studio Code. Visual Studio Code est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utiliserez cet éditeur ultérieurement dans ce didacticiel pour modifier l’exemple de code.

<a id="summary" class="xliff"></a>

## Résumé
Vous avez installé les outils de développement et le logiciel nécessaire pour le premier exemple d’application. La tâche suivante consiste à créer, déployer et exécuter l’exemple d’application sur Edison.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Création et déploiement de l’exemple d’application de clignotement][create-and-deploy-the-blink-application]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-node-lesson1-deploy-blink-app.md
[windows]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-mac.md

