---
title: "Connecter Raspberry Pi (C) à Azure IoT - Leçon 2 : Outils Azure (macOS) | Microsoft Docs"
description: "Sur Mac OS, installez Python et l’interface de ligne de commande Azure (Azure CLI)."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: iot cloud service, azure cli
ms.assetid: f2d7d584-7734-401c-976c-81788a7282a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: f0615f10adaf3bd03bc9a3b446830f2906875a89
ms.lasthandoff: 01/24/2017


---
# <a name="get-azure-tools-macos-1010"></a>Obtenir les outils Azure (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 et versions ultérieures](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>Procédure à suivre
Installez l’interface de ligne de commande Azure (Azure CLI). Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :
* Comment installer Azure CLI.
* Comment ajouter un sous-groupe IoT de l’interface de ligne de commande Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin
* Un Mac avec une connexion Internet.
* Un abonnement Azure actif. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte d’évaluation Azure gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

## <a name="install-python"></a>Installer python
Bien que Mac OS soit fourni avec Python 2.7, il est recommandé d’installer Python via Homebrew. Voir [Installation de Python sur Mac OS](http://docs.python-guide.org/en/latest/starting/install/osx/).

Installez Python et pip en exécutant la commande suivante :

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure
L’interface de ligne de commande Azure offre une expérience de ligne de commande multiplateforme pour Azure. Vous travaillez directement à partir de votre ligne de commande pour approvisionner et gérer les ressources. 

Pour installer la dernière version d’Azure CLI, procédez comme suit :

1. Exécutez les commandes suivantes dans une fenêtre de Terminal. L’installation de l’interface de ligne de commande Azure peut prendre cinq minutes.

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
2. Vérifiez l’installation en exécutant la commande suivante :

   ```bash
   az iot -h
   ```

Si l’installation a réussi, vous devez voir la sortie suivante.

![Sortie indiquant la réussite](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_osx.png)

## <a name="summary"></a>Résumé
Vous avez installé l’interface de ligne de commande Azure. La tâche suivante consiste à créer votre Azure IoT Hub et votre identité d’appareil à l’aide de l’interface de ligne de commande Azure.

## <a name="next-steps"></a>Étapes suivantes
[Créer votre IoT Hub et inscrire Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md)


