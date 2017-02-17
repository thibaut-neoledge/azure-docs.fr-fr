---
title: "Appareil simulé et passerelle Azure IoT - Leçon 3 : Lire des messages | Microsoft Docs"
description: "Exécutez un exemple de code sur votre ordinateur hôte pour lire les messages à partir de votre IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "données dans le cloud, collecte de données cloud, service de cloud iot, données iot"
ms.assetid: 5a6ec9c1-d83c-41c1-beaf-7c0d3395d77f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 08ccd35c6e6a8e77f0fd4637f14a1f27730560d6


---

# <a name="read-messages-from-your-iot-hub"></a>Lire des messages à partir de votre IoT Hub

## <a name="what-you-will-do"></a>Procédure à suivre

- Exécutez l’exemple de code sur votre ordinateur hôte pour lire les messages provenant de votre IoT Hub.

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu

Découvrez comment utiliser l’outil gulp pour lire les messages à partir de votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- L'exemple d'appareil simulé de la rubrique [Configurer et exécuter un exemple d’application de téléchargement cloud d’appareil simulé](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obtenir vos chaînes de connexion d’IoT Hub et d’appareil

La chaîne de connexion de l’appareil permet de connecter votre appareil simulé à votre IoT Hub. La chaîne de connexion IoT Hub sert à se connecter au registre des identités de votre IoT Hub pour gérer les appareils autorisés à se connecter à votre IoT Hub.

- Répertorier tous les IoT Hubs de votre groupe de ressources en exécutant la commande suivante :

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   Si vous n’avez pas modifié la valeur, utilisez `iot-gateway` en tant que valeur de `{resource group name}`.
- Obtenez la chaîne de connexion de l'IoT Hub en exécutant la commande suivante :

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` est le nom que vous avez spécifié à la leçon 2.

## <a name="configure-the-device-connection-for-the-sample-code"></a>Configurer la connexion de l’appareil pour l'exemple de code

Mettez à jour les configurations de connexion de l'appareil IoT Hub dans `config-azure.json` en effectuant les opérations suivantes :

1. Ouvrez `config-azure.json` dans Visual Studio Code en exécutant la commande suivante dans une fenêtre de console :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. Dans le fichier `config-azure.json`, effectuez les remplacements suivants :

   ![capture d’écran de configuration azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Remplacez `[IoT hub connection string]` par la chaîne de connexion de l'IoT Hub.

## <a name="read-messages-from-your-iot-hub"></a>Lire des messages à partir de votre IoT Hub

Exécutez l’exemple d’application d'appareil simulé et lisez les messages de l'IoT Hub à l'aide de la commande suivante :

```bash
gulp run --iot-hub
```

La commande exécute l’application qui envoie des messages à votre IoT Hub toutes les 2 secondes. Elle génère également un processus enfant pour recevoir le message.

Les messages envoyés et reçus sont tous affichés instantanément sur la même fenêtre de console sur l’ordinateur hôte. L’application va s’arrêter dans 40 secondes.

![Exemple d’application d'appareil simulé avec des messages envoyés et reçus](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub_simudev.png)

## <a name="summary"></a>Résumé

Vous avez correctement exécuté l’exemple d’application pour envoyer des données à votre IoT Hub à l'aide d'un appareil simulé. Vous avez également lu les messages qui ont été envoyés à votre IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Créer une application de fonction Azure et un compte de stockage Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)





<!--HONumber=Jan17_HO4-->


