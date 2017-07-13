---
title: "Connecter Intel Edison (Node) à Azure IoT - Leçon 3 : Surveiller les messages | Microsoft Docs"
description: "Surveillez les messages appareil-à-cloud alors qu’ils sont écrits dans votre stockage Table Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "données dans le cloud, collecte de données cloud, service cloud iot, données iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: fa2c7efe-7e34-4e39-bb70-015c15ac69ed
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 5100e6dbd74095f419c1017a3784fac547bf200b
ms.contentlocale: fr-fr
ms.lasthandoff: 01/25/2017

---
<a id="read-messages-persisted-in-azure-storage" class="xliff"></a>

# Lecture des messages conservés dans le stockage Azure
<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre
Surveillez les messages appareil-à-cloud qui sont envoyés à partir d’Intel Edison à votre IoT Hub, à mesure que les messages sont écrits dans votre stockage Table Azure. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
Dans cet article, vous allez apprendre à utiliser la tâche de lecture de message gulp pour lire les messages conservés dans votre stockage Table Azure.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin
Avant de commencer, vous devez avoir correctement suivi la section [Exécution de l’exemple d’application de clignotement Azure sur Intel Edison][run-the-azure-blink-sample-application-on-intel-edison].

<a id="read-new-messages-from-your-storage-account" class="xliff"></a>

## Lecture des nouveaux messages à partir de votre compte de stockage
Dans l’article précédent, vous avez exécuté un exemple d’application sur Edison. L’exemple d’application a envoyé des messages à votre Azure IoT Hub. Les messages envoyés à votre IoT Hub sont stockés dans votre stockage Table Azure via l’application de fonction Azure. Pour lire les messages à partir de votre stockage Table Azure, vous avez besoin de la chaîne de connexion du Stockage Azure.

Pour lire les messages stockés dans votre stockage Table Azure, procédez comme suit :

1. Obtenez la chaîne de connexion en exécutant la commande suivante :

   ```bash
   az storage account list -g iot-sample --query [].name
   az storage account show-connection-string -g iot-sample -n {storage name}
   ```

   La première commande récupère le `storage name` utilisé dans la deuxième commande pour obtenir la chaîne de connexion. Si vous n’avez pas modifié la valeur, utilisez `iot-sample` en tant que valeur de `{resource group name}`.
2. Ouvrez le fichier de configuration `config-edison.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```
3. Remplacez `[Azure storage connection string]` par la chaîne de connexion que vous avez obtenue à l’étape 1.
4. Enregistrez le fichier `config-edison.json`.
5. Envoyez de nouveau des messages et lisez-les à partir de votre stockage Table Azure en exécutant la commande suivante :

   ```bash
   gulp run --read-storage
   ```

   La logique pour la lecture à partir du stockage Table Azure se trouve dans le fichier `azure-table.js`.

   ![exécutez gulp --lire-stockage][gulp run]

<a id="summary" class="xliff"></a>

## Résumé
Vous avez correctement connecté Edison à votre IoT Hub dans le cloud et utilisé l’exemple d’application de clignotement pour envoyer des messages appareil-à-cloud. Vous avez également utilisé l’application de fonction Azure pour stocker des messages d’IoT Hub entrants votre stockage Table Azure. Vous pouvez désormais envoyer des messages cloud-à-appareil depuis votre IoT Hub vers Edison.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Exécution d’un exemple d’application pour recevoir des messages cloud-à-appareil][receive-cloud-to-device-messages]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[run-the-azure-blink-sample-application-on-intel-edison]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
[gulp run]: media/iot-hub-intel-edison-lessons/lesson3/gulp_read_message.png
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
