---
title: "Appareil SensorTag et passerelle Azure IoT - Leçon 2 : Obtenir des outils (Windows) | Microsoft Docs"
description: "Installez les outils et les logiciels sur votre ordinateur hôte exécutant Windows, créez un hub IoT et inscrivez votre appareil dans l’IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "développement iot, logiciel iot, service cloud iot, logiciel internet des objets, azure cli, installer git sur windows, exécuter gulp, installer node js windows, installer npm sur windows, installer python sur windows"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 18ae6ee4-574a-4d5f-9838-ca2a78165628
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 4a854222be9aaf6733f1c256dd75195927299099
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-windows-7-and-later"></a>Obtenir les outils (Windows 7 et versions ultérieures)
> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Procédure à suivre

- Installation de Git, Node.js, Gulp et Python.
- Installez l’interface de ligne de commande Azure (Azure CLI). 

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu

Dans cette leçon, vous allez apprendre ce qui suit :

- Installation de [Git](https://git-scm.com/) et [Node.js](https://nodejs.org/en/).
  - Git est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cette leçon est stocké sur Git.
  - Node.js est un runtime JavaScript avec un écosystème de packages riche.
- Utilisation de [NPM](https://www.npmjs.com/) pour installer des outils de développement Node.js.
  - La version minimale requise de Node.js est 4.5 LTS.
  - NPM est l’un des gestionnaires de packages pour Node.js.
- Installation de Visual Studio Code.
  - Visual Studio Code est un éditeur de code source multiplateforme simple mais puissant pour Windows, Linux et macOS. Il offre une aide appréciable pour le débogage, le contrôle Git incorporé, la mise en surbrillance de syntaxe, la complétion de code intelligente, les extraits et la refactorisation de code.
- Comment installer Python.
  - Python est un langage de programmation interprété et dynamique, à usage général et fréquemment utilisé.
- Installation de l’interface de ligne de commande Azure.
  - L’interface de ligne de commande Azure offre une expérience de ligne de commande multiplateforme pour Azure. Vous travaillez directement à partir d’une ligne de commande pour approvisionner et gérer les ressources.
- Utilisation de l’interface de ligne de commande Azure pour créer un IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Une connexion Internet pour télécharger les outils et le logiciel.
- Un ordinateur Windows.

## <a name="install-git-and-nodejs"></a>Installation de Git et Node.js

Cliquez sur les liens ci-dessous pour télécharger et installer Git et Node.js LTS pour Windows.

- [Téléchargement de Git pour Windows](https://git-scm.com/download/win/)
- [Téléchargement de Node.js LTS pour Windows](https://nodejs.org/en/)

## <a name="install-nodejs-development-tools"></a>Installer des outils de développement Node.js

Vous utilisez [gulp.js](http://gulpjs.com/) pour automatiser le déploiement et l’exécution de scripts.

Appuyez sur `Windows + R`, saisissez `cmd` et appuyez sur `Enter` pour ouvrir une fenêtre d’invite de commandes, puis exécutez la commande suivante :

```cmd
npm install -g gulp
```

Si vous rencontrez des problèmes lors de l’installation, consultez le [guide de dépannage](iot-hub-gateway-kit-c-troubleshooting.md) des problèmes courants.

> [!Note]
> Node, NPM et Gulp sont requis pour exécuter des scripts d’automatisation développés dans Node.js.

## <a name="install-python"></a>Installer python

Vous pouvez choisir entre Python 2.7, 3.4 ou 3.5. Dans ce didacticiel, nous utilisons Python 2.7. Si vous avez déjà installé Python, passez à la section suivante.

[Obtenir Python pour Windows](https://www.python.org/downloads/)

Vous devez également ajouter le chemin d’accès des dossiers où Python.exe et pip.exe sont installés à la variable d’environnement `PATH` du système. Par défaut, python.exe est installé dans `C:\Python27`, et pip.exe dans `C:\Python27\Scripts`.

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure

Pour installer l’interface de ligne de commande Azure, procédez comme suit :

1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.

2. Installez l’interface de ligne de commande Azure en exécutant les commandes suivantes :

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```

   L’installation peut prendre 5 minutes.

3. Vérifiez l’installation en exécutant la commande suivante :

   ```cmd
   az iot -h
   ```

   Si l’installation a réussi, vous devez voir la sortie suivante.

   ![Vérifier l’installation de l’interface de ligne de commande Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_win.png)

## <a name="install-visual-studio-code"></a>Installation de Visual Studio Code

Visual Studio Code, plus loin dans ce didacticiel, vous permet de modifier les fichiers de configuration.

[Téléchargez](https://code.visualstudio.com/docs/setup/windows) et installez Visual Studio Code.

## <a name="summary"></a>Résumé

Vous avez installé tous les outils et logiciels nécessaires sur votre ordinateur hôte. La tâche suivante consiste à utiliser l’interface de ligne de commande Azure pour créer un IoT Hub et inscrire votre appareil dans votre IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Créer un hub IoT et enregistrer votre appareil](iot-hub-gateway-kit-c-lesson2-register-device.md)

