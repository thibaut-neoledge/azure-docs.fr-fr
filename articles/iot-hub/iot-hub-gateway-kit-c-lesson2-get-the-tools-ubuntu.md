---
title: "Appareil SensorTag et passerelle Azure IoT - Leçon 2 : Obtenir des outils (Ubuntu) | Microsoft Docs"
description: "Installez les outils et les logiciels sur votre ordinateur hôte exécutant Ubuntu, créez un IoT Hub et inscrivez votre appareil dans l’IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "développement iot, logiciel iot, service cloud iot, logiciel internet des objets, azure cli, installer git sur ubuntu, exécuter gulp, installer node js ubuntu"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 0bac1412-385b-4255-a33f-9d44c35feb3e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 6414a4f37367d8acc0bab9356b8a09aadf13bfc9
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-ubuntu-1604"></a>Obtenir les outils (Ubuntu 16.04)
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

- Installation de Git et Node.js.
  - Git est un système de contrôle de versions distribué open source très populaire. L’exemple d’application de cette leçon est stocké sur Git.
  - Node.js est un runtime JavaScript avec un écosystème de packages riche.
- Comment utiliser NPM pour installer des outils de développement Node.js.
  - La version minimale requise de Node.js est 4.5 LTS.
  - NPM est l’un des gestionnaires de packages pour Node.js.
- Installation de Visual Studio Code.
  - Visual Studio Code est un éditeur de code source multiplateforme simple mais puissant pour Windows, Linux et macOS. Il offre une aide appréciable pour le débogage, le contrôle Git incorporé, la mise en surbrillance de syntaxe, la complétion de code intelligente, les extraits et la refactorisation de code.
- Installation de l’interface de ligne de commande Azure
  - L’interface de ligne de commande Azure offre une expérience de ligne de commande multiplateforme pour Azure. Vous travaillez directement à partir d’une ligne de commande pour approvisionner et gérer les ressources.
- Utilisation de l’interface de ligne de commande Azure pour créer un IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Une connexion Internet pour télécharger les outils et le logiciel.
- Un ordinateur exécutant Ubuntu 16.04 ou version ultérieure.

## <a name="install-git-and-nodejs"></a>Installation de Git et Node.js

Pour installer Git et Node.js, procédez comme suit :

1. Appuyez sur `Ctrl + Alt + T` pour ouvrir un terminal.
2. Exécutez les commandes suivantes :

   ```bash
   sudo apt-get update
   curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
   sudo apt-get install -y nodejs
   sudo apt-get install git
   ```

## <a name="install-nodejs-development-tools"></a>Installer des outils de développement Node.js

Vous utilisez [gulp.js](http://gulpjs.com/) pour automatiser le déploiement et l’exécution de scripts.

Pour installer gulp, exécutez la commande suivante dans le terminal :

```bash
sudo npm install -g gulp
```

Si vous rencontrez des problèmes lors de l’installation, consultez le [guide de dépannage](iot-hub-gateway-kit-c-troubleshooting.md) des problèmes courants.

> [!Note]
> Node, NPM et Gulp sont requis pour exécuter des scripts d’automatisation développés dans Node.js.

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure

Pour installer l’interface de ligne de commande Azure, procédez comme suit :

1. Exécutez les commandes suivantes dans le terminal :

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```

   L’installation peut prendre 5 minutes.

2. Vérifiez l’installation en exécutant la commande suivante :

   ```bash
   az iot -h
   ```
Si l’installation a réussi, vous devez voir la sortie suivante.
![Vérifier l’installation de l’interface de ligne de commande Azure](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_ubuntu.png)

### <a name="install-visual-studio-code"></a>Installation de Visual Studio Code

Visual Studio Code, plus loin dans ce didacticiel, vous permet de modifier les fichiers de configuration.

[Téléchargez](https://code.visualstudio.com/docs/setup/linux) et installez Visual Studio Code.

## <a name="summary"></a>Résumé

Vous avez installé tous les outils et logiciels nécessaires sur votre ordinateur hôte. La tâche suivante consiste à utiliser l’interface de ligne de commande Azure pour créer un IoT Hub et inscrire votre appareil dans votre IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Créer un hub IoT et enregistrer votre appareil](iot-hub-gateway-kit-c-lesson2-register-device.md)

