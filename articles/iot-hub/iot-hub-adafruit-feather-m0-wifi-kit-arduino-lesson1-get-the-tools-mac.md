---
title: "Connecter Arduino à Azure IoT - Leçon 1 : Obtenir les outils (macOS) | Microsoft Docs"
description: "Téléchargez et installez les outils et logiciels nécessaires pour le premier exemple d’application pour Adafruit Feather M0 WiFi sur macOS."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "outils de développement arduino, développement iot, logiciel iot, logiciel internet des objets, installer git sur mac, installer node js mac"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 0262f3dd-0259-4eb0-962f-9fb534f8359e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 41b2277a26f0e99052a699cfab8a8129a6c7ddb1
ms.contentlocale: fr-fr
ms.lasthandoff: 01/24/2017

---
<a id="get-the-tools-macos-1010" class="xliff"></a>

# Obtenir les outils (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre

Téléchargez les outils de développement et le logiciel pour le premier exemple d’application pour votre carte Adafruit Feather M0 WiFi Arduino. 

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

> [!NOTE]
> Le langage de programmation de la logique principale est Arduino. Cependant, les outils Node.js sont utilisés dans cette leçon pour créer et déployer des exemples d’applications.

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
Cet article portera sur les éléments suivants :

* Installation de Git et Node.js.
  * [Git](https://git-scm.com) est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cet article est stocké sur Git.
  * [Node.js](https://nodejs.org/en/) est un runtime JavaScript avec un écosystème de packages riche.
* Comment utiliser NPM pour installer des outils de développement Node.js supplémentaires.
  * La version minimale requise de Node.js est 4.5 LTS.
  * [NPM](https://www.npmjs.com) est l’un des gestionnaires de packages pour Node.js.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin
Pour effectuer cette opération, vous avez besoin des éléments suivants :
* Une connexion Internet pour télécharger les outils de développement et le logiciel.
* Un Mac exécutant macOS Yosemite (10.10) ou version ultérieure.

<a id="install-git-and-nodejs" class="xliff"></a>

## Installation de Git et Node.js
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

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## Installation des outils de développement Node.js supplémentaires
Utilisez [gulp.js](http://gulpjs.com) pour automatiser le déploiement de l’exemple d’application sur votre carte Arduino.

Installez `gulp`, `device-discovery-cli` en exécutant la commande suivante dans le terminal :

```bash
sudo npm install -g gulp device-discovery-cli
```

Si vous rencontrez des problèmes pour installer Node.js et ces outils de développement supplémentaires sur macOS, pour trouver des solutions aux problèmes courants, voir le [guide de dépannage][troubleshooting].

<a id="install-visual-studio-code" class="xliff"></a>

## Installation de Visual Studio Code
[Téléchargez](https://code.visualstudio.com/docs/setup/osx) et installez Visual Studio Code. Visual Studio Code est un éditeur de code source simple mais puissant pour Windows, Linux et Mac OS. Vous utiliserez cet éditeur ultérieurement dans ce didacticiel pour modifier l’exemple de code.

<a id="summary" class="xliff"></a>

## Résumé
Vous avez installé les outils de développement et le logiciel nécessaire pour le premier exemple d’application. La tâche suivante consiste à créer, déployer et exécuter l’exemple d’application sur votre carte Arduino.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Créer et déployer l’application Blink][create-and-deploy-the-blink-application]
<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-mac.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
