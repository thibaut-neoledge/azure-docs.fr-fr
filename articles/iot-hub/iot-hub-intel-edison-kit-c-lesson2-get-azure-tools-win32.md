---
title: "Connecter Intel Edison (C) à Azure IoT - Leçon 2 : Outils Azure (Windows) | Microsoft Docs"
description: "Sur Windows 7 et versions ultérieures, installez Python et l’interface de ligne de commande Azure (Azure CLI)."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: interface de ligne de commande azure, service cloud iot, cloud arduino
ms.assetid: 1035760e-cdd1-4d99-8003-067e98b29762
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: c31847b0a4b630f999f64c139f6b9b44623d847e
ms.lasthandoff: 01/25/2017


---
# <a name="get-azure-tools-windows-7-and-later"></a>Obtenir les outils Azure (Windows 7 et versions ultérieures)
> [!div class="op_single_selector"]
> * [Windows 7 et versions ultérieures][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>Procédure à suivre
Installez Python et l’interface de ligne de commande Azure (Azure CLI). Si vous rencontrez des problèmes, recherchez des solutions sur la [page de résolution des problèmes][troubleshooting].

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :
* Comment installer Python.
* Installation de l’interface de ligne de commande Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin
* Un ordinateur Windows connecté à Internet.
* Un abonnement Azure actif. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte d’évaluation Azure gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

## <a name="install-python"></a>Installation de Python
[Installez Python](https://www.python.org/downloads/) sur votre ordinateur Windows. Vous pouvez installer Python 2.7, 3.4 ou 3.5. Ce didacticiel est basé sur Python 2.7. Si vous avez déjà installé Python, accédez à la section suivante et installez l’interface de ligne de commande Azure.

Vous devez également ajouter le chemin d’accès des dossiers où python.exe et pip.exe sont installés à la variable d’environnement `PATH` du système. Par défaut, python.exe est installé dans `C:\Python27`, et pip.exe dans `C:\Python27\Scripts`.

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure
L’interface de ligne de commande Azure offre une expérience de ligne de commande multiplateforme pour Azure. Vous travaillez directement à partir de votre ligne de commande pour configurer et gérer les ressources.

Pour installer l’interface de ligne de commande Azure, procédez comme suit :

1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Exécutez les commandes suivantes :

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. Vérifiez l’installation en exécutant la commande suivante :

   ```cmd
   az iot -h
   ```

Si l’installation a réussi, vous voyez la sortie suivante.

![Sortie qui indique la réussite](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_win.png)

## <a name="summary"></a>Résumé
Vous avez installé l’interface de ligne de commande Azure. La tâche suivante consiste à créer votre Azure IoT Hub et votre identité d’appareil à l’aide de l’interface de ligne de commande Azure.

## <a name="next-steps"></a>Étapes suivantes
[Créer votre IoT Hub et inscrire Intel Edison][create-your-iot-hub-and-register-intel-edison]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-mac.md

