---
featureFlags:
- usabilla
title: "Connecter Raspberry Pi (Node) à Azure IoT - Leçon 3 : Stockage de table | Microsoft Docs"
description: "Surveillez les messages appareil-à-cloud alors qu’ils sont écrits dans votre stockage Table Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "récupérer des données du cloud, service cloud iot"
ms.assetid: 9965bd54-61da-479b-aaad-5604850a2be5
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 0e35ffda2d5f6698c4e9d96f1991998b36a7f230
ms.lasthandoff: 01/24/2017


---
# <a name="read-messages-persisted-in-azure-storage"></a>Lecture des messages conservés dans le stockage Azure
## <a name="what-you-will-do"></a>Procédure à suivre
Surveillez les messages appareil-à-cloud qui sont envoyés à partir de Raspberry Pi 3 à votre IoT Hub, à mesure que les messages sont écrits dans votre stockage Table Azure. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu
Dans cet article, vous allez apprendre à utiliser la tâche de lecture de message gulp pour lire les messages conservés dans votre stockage Table Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Avant de commencer, vous devez avoir correctement suivi la section [Exécution de l’exemple d’application de clignotement Azure sur Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md).

## <a name="read-new-messages-from-your-storage-account"></a>Lecture des nouveaux messages à partir de votre compte de stockage
Dans l’article précédent, vous avez exécuté un exemple d’application sur Pi. L’exemple d’application a envoyé des messages à votre Azure IoT Hub. Les messages envoyés à votre IoT Hub sont stockés dans votre stockage Table Azure via l’application de fonction Azure. Pour lire les messages à partir de votre stockage Table Azure, vous avez besoin de la chaîne de connexion du Stockage Azure.

Pour lire les messages stockés dans votre stockage Table Azure, procédez comme suit :

1. Obtenez la chaîne de connexion en exécutant la commande suivante :

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   La première commande récupère le `storage name` utilisé dans la deuxième commande pour obtenir la chaîne de connexion. Si vous n’avez pas modifié la valeur, utilisez `iot-sample` en tant que valeur de `{resource group name}`.
2. Ouvrez le fichier de configuration `config-raspberrypi.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
   
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-raspberrypi.json
   ```
3. Remplacez `[Azure storage connection string]` par la chaîne de connexion que vous avez obtenue à l’étape 1.
4. Enregistrez le fichier `config-raspberrypi.json`.
5. Envoyez de nouveau des messages et lisez-les à partir de votre stockage Table Azure en exécutant la commande suivante :
   
   ```bash
   gulp run --read-storage
   ```
   
   La logique pour la lecture à partir du stockage Table Azure se trouve dans le fichier `azure-table.js`.
   
    ![exécutez gulp --lire-stockage](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message.png)

## <a name="summary"></a>Résumé
Vous avez correctement connecté Pi à votre IoT Hub dans le cloud et utilisé l’exemple d’application de clignotement pour envoyer des messages appareil-à-cloud. Vous avez également utilisé l’application de fonction Azure pour stocker des messages d’IoT Hub entrants votre stockage Table Azure. Vous pouvez désormais envoyer des messages cloud-à-appareil depuis votre IoT Hub vers Pi.

## <a name="next-steps"></a>Étapes suivantes
[Exécution de l’exemple d’application pour recevoir des messages cloud-à-appareil](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)


