---
title: "Connecter Intel Edison (C) à Azure IoT - Leçon 1 : Obtenir des outils (Windows) | Microsoft Docs"
description: "Téléchargez et installez les outils et logiciels nécessaires pour le premier exemple d’application pour Edison sur Windows 7 et versions ultérieures."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "outils de développement arduino, développement iot, logiciel iot, logiciel internet des objets, installer git sous windows, installer node js sous windows"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 7d29a358-544d-4657-a504-5ed9b79c2925
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 79dcbe8cbefe4f607991359fbfc6948b5e90ace9
ms.contentlocale: fr-fr
ms.lasthandoff: 01/25/2017

---
<a id="get-the-tools-windows-7-or-later" class="xliff"></a>

# Obtenir les outils (Windows 7 ou version ultérieure)
> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre
Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour Intel Edison. Si vous rencontrez des problèmes, recherchez des solutions sur la [page de résolution des problèmes][troubleshooting].

> [!NOTE]
> Le langage de programmation de la logique principale est C. Cependant, les outils Node.js sont utilisés dans cette leçon pour créer et déployer des exemples d’applications.

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
Cet article portera sur les éléments suivants :

* Installation de Git et Node.js.
  * [Git](https://git-scm.com) est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cet article est stocké sur Git.
  * [Node.js](https://nodejs.org/en/) est un runtime JavaScript avec un écosystème de packages riche.
* Comment utiliser NPM pour installer des outils de développement Node.js supplémentaires.
  * La version minimum requise de Node.js est 4.5 LTS.
  * [NPM](https://www.npmjs.com) est l’un des gestionnaires de packages pour Node.js.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin

Pour effectuer cette opération, vous avez besoin des éléments suivants :

* Une connexion Internet pour télécharger les outils de développement et le logiciel.
* Un ordinateur fonctionnant sous Windows.

<a id="install-git-and-nodejs" class="xliff"></a>

## Installation de Git et Node.js

Cliquez sur les liens ci-dessous pour télécharger et installer Git et Node.js LTS pour Windows.

* [Téléchargement de Git pour Windows](https://git-scm.com/download/win/)
* [Téléchargement de Node.js LTS pour Windows](https://nodejs.org/en/)

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## Installation des outils de développement Node.js supplémentaires

Utilisez [gulp.js](http://gulpjs.com) pour automatiser le déploiement de l’exemple d’application sur Edison.

Lancez une invite de commandes en tant qu’administrateur. Installez `gulp` en exécutant la commande suivante :

```cmd
npm install -g gulp
```

Si vous rencontrez des problèmes pour installer Node.js et ces outils de développement Node.js supplémentaires sur votre ordinateur, consultez le [guide de résolution des problèmes][troubleshooting] pour trouver une solution aux problèmes courants.

<a id="install-visual-studio-code" class="xliff"></a>

## Installation de Visual Studio Code

[Téléchargez](https://code.visualstudio.com/docs/setup/windows) et installez Visual Studio Code. Visual Studio Code est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utiliserez cet éditeur ultérieurement dans ce didacticiel pour modifier l’exemple de code.

<a id="summary" class="xliff"></a>

## Résumé

Vous avez installé les outils de développement et le logiciel nécessaire pour le premier exemple d’application. La tâche suivante consiste à créer, déployer et exécuter l’exemple d’application sur Edison.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

[Créer et déployer l’application Blink][create-and-deploy-the-blink-application]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-c-lesson1-deploy-blink-app.md
[windows]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-mac.md

