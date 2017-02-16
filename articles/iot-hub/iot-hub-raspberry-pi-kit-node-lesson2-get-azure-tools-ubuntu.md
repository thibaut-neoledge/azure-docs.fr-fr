---
title: Obtenir les outils Azure (Ubuntu 16.04) | Microsoft Docs
description: "Sur Ubuntu, installez Python et l’interface de ligne de commande Azure (Azure CLI)."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: iot cloud service, azure cli
ms.assetid: 2f98923a-5274-4220-87d4-77ac8beb4d0f
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 7fbf21434952ece22468df6873e10e6f3eae1724
ms.openlocfilehash: cf1abb14f5a9e5658fdfb348c867b5c4097b68eb


---
# <a name="get-azure-tools-ubuntu-1604"></a>Obtenir les outils Azure (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 et versions ultérieures](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>Procédure à suivre
Installez l’interface de ligne de commande Azure (Azure CLI). Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :
* Installation de l’interface de ligne de commande Azure.
* Ajout d’un sous-groupe IoT de l’interface de ligne de commande Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin
* Un ordinateur Ubuntu connecté à Internet.
* Un abonnement Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte d’évaluation gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure
L’interface de ligne de commande Azure fournit une expérience de ligne de commande multiplateforme pour Azure, permettant de travailler directement à partir de la ligne de commande pour configurer et gérer des ressources.

Pour installer la dernière version d’Azure CLI, procédez comme suit :

1. Exécutez les commandes suivantes dans une fenêtre de Terminal. L’installation de l’interface de ligne de commande Azure peut prendre cinq minutes.

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```
2. Vérifiez l’installation en exécutant la commande suivante :

   ```bash
   az iot -h
   ```

Si l’installation a réussi, vous devez voir la sortie suivante.

![Sortie indiquant la réussite](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_ubuntu.png)

## <a name="summary"></a>Résumé
Vous avez installé l’interface de ligne de commande Azure. La tâche suivante consiste à créer votre Azure IoT Hub et votre identité d’appareil à l’aide de l’interface de ligne de commande Azure.

## <a name="next-steps"></a>Étapes suivantes
[Créer votre IoT Hub et inscrire Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)




<!--HONumber=Dec16_HO1-->


