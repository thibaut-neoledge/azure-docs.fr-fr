---
title: "Préparer votre ordinateur Mac et Azure IoT Hub | Microsoft Docs"
description: "Installez les outils sur votre ordinateur Mac, créez un IoT Hub et inscrivez votre appareil dans l’IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "développement iot, logiciel iot, service cloud iot, logiciel internet des objets, azure cli, installer python mac, installer git sur mac, exécuter gulp, installer node js mac"
ms.assetid: 94e538ef-9857-4023-9c3c-e92a0be7c395
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: 90b211e1d0b85e2c99ab31a633ddcee9218d4874


---
# <a name="get-the-tools-macos"></a>Obtenir les outils (Mac OS)
> [!div class="op_single_selector"]
> * [Windows 7 ou version ultérieure](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>Procédure à suivre

- Installation de Git, Node.js, Gulp et Python.
- Installez l’interface de ligne de commande Azure (Azure CLI). 

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu

Dans cette leçon, vous allez apprendre ce qui suit :

- Installation de [Git](https://git-scm.com/) et [Node.js](https://nodejs.org/en/).
  - Git est un système de contrôle de versions distribué open source. L’exemple d’application de cette leçon est stocké sur Git.
  - Node.js est un runtime JavaScript avec un écosystème de packages riche.
- Utilisation de [NPM](https://www.npmjs.com/) pour installer des outils de développement Node.js.
  - La version minimale requise de Node.js est 4.5 LTS.
  - NPM est l’un des gestionnaires de packages pour Node.js.
- Installation de Visual Studio Code.
  - Visual Studio Code est un éditeur de code source multiplateforme simple mais puissant pour Windows, Linux et Mac OS. Il offre une aide appréciable pour le débogage, le contrôle Git incorporé, la mise en surbrillance de syntaxe, la complétion de code intelligente, les extraits et la refactorisation de code.
- Comment installer Python.
  - Python est un langage de programmation interprété et dynamique, à usage général et fréquemment utilisé.
- Installation de l’interface de ligne de commande Azure.
  - L’interface de ligne de commande Azure offre une expérience de ligne de commande multiplateforme pour Azure. Vous travaillez directement à partir d’une ligne de commande pour approvisionner et gérer les ressources.
- Utilisation de l’interface de ligne de commande Azure pour créer un IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Une connexion Internet pour télécharger les outils et le logiciel.
- Un ordinateur Mac exécutant OS X Yosemite (10.10) ou une version ultérieure.

## <a name="install-git-and-nodejs"></a>Installation de Git et Node.js

Pour installer Git et Node.js, servez-vous de l’utilitaire de gestion de package Homebrew en procédant comme suit :

1. [Téléchargez](http://brew.sh/) et installez Homebrew. Si vous avez déjà installé Homebrew, passez à l’étape 2.
   1. Appuyez sur `Cmd + Space` puis entrez `Terminal` pour ouvrir une fenêtre de terminal.
   2. Exécutez la commande suivante :

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```

2. Installez de Git et Node.js en exécutant la commande suivante :

    ```bash
    brew install node git
    ```

## <a name="install-nodejs-development-tools"></a>Installer des outils de développement Node.js

Vous utilisez [gulp.js](http://gulpjs.com/) pour automatiser le déploiement et l’exécution de scripts.

Pour installer gulp, exécutez la commande suivante dans le terminal :

```bash
npm install -g gulp
```

Si vous rencontrez des problèmes lors de l’installation, consultez le [guide de dépannage](iot-hub-gateway-kit-c-troubleshooting.md) des problèmes courants.

> [!Note]
> Node, NPM et Gulp sont requis pour exécuter des scripts d’automatisation développés dans Node.js.

## <a name="install-python"></a>Installer python

Bien que Mac OS X soit fourni avec Python 2.7, il est recommandé d’installer Python via Homebrew. Consultez [Installation de Python sur Mac OS X](http://docs.python-guide.org/en/latest/starting/install/osx/).

Installez Python et pip en exécutant la commande suivante :

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure

Pour installer l’interface de ligne de commande Azure, procédez comme suit :

1. Exécutez les commandes suivantes dans le terminal :
   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
   L’installation peut prendre 5 minutes.

2. Vérifiez l’installation en exécutant la commande suivante :
   ```bash
   az iot -h
   ```
   Si l’installation a réussi, vous devez voir la sortie suivante.

   ![Vérifier l’installation de l’interface de ligne de commande Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_osx.png)

## <a name="install-visual-studio-code"></a>Installation de Visual Studio Code

Visual Studio Code, plus loin dans ce didacticiel, vous permet de modifier les fichiers de configuration.

[Téléchargez](https://code.visualstudio.com/docs/setup/osx) et installez Visual Studio Code.

## <a name="summary"></a>Résumé

Vous avez installé tous les outils et logiciels nécessaires sur votre ordinateur Mac. La tâche suivante consiste à utiliser l’interface de ligne de commande Azure pour créer un IoT Hub et inscrire votre appareil dans votre IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Créer un IoT Hub et inscrire l’appareil](iot-hub-gateway-kit-c-lesson2-register-device.md)



<!--HONumber=Dec16_HO3-->


