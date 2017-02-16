---
title: "Lire des messages à partir de votre Azure IoT Hub | Microsoft Docs"
description: "Exécutez un exemple de code sur votre ordinateur hôte pour lire les messages à partir de votre IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "données dans le cloud, collecte de données cloud, service de cloud iot, données iot"
ms.assetid: cc88be24-b5c0-4ef2-ba21-4e8f77f3e167
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: 09b0e0321b08a11622633e727cf8be54c1707436


---

# <a name="read-messages-from-your-iot-hub"></a>Lire des messages à partir de votre IoT Hub

## <a name="what-you-will-do"></a>Procédure à suivre

- Exécutez l’exemple de code sur votre ordinateur hôte pour lire les messages provenant de votre IoT Hub.

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu

Découvrez comment utiliser l’outil gulp pour lire les messages à partir de votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- L’exemple d’application BLE que vous avez exécuté dans la leçon 3.

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obtenir vos chaînes de connexion d’IoT Hub et d’appareil

La chaîne de connexion de l’appareil permet de connecter votre appareil (TI SensorTag ou appareil simulé) à votre IoT Hub. La chaîne de connexion IoT Hub sert à se connecter au registre des identités de votre IoT Hub pour gérer les appareils autorisés à se connecter à votre IoT Hub.

- Répertoriez tous les IoT Hubs de votre groupe de ressources en exécutant la commande suivante :

   ```bash
   az iot hub list -g iot-gateway --query [].name
   ```

   Si vous n’avez pas modifié la valeur, utilisez `iot-gateway` en tant que valeur de `{resource group name}`.
- Obtenez la chaîne de connexion de l'IoT Hub en exécutant la commande suivante :

   ```bash
   az iot hub show-connection-string --name {my hub name} -g iot-gateway
   ```

   `{my hub name}` est le nom que vous avez spécifié à la leçon 2.

## <a name="configure-the-device-connection-for-the-sample-code"></a>Configurer la connexion de l’appareil pour l'exemple de code

Mettez à jour le fichier de configuration d’appareil `config-azure.json` pour lire les messages de votre IoT Hub sur votre ordinateur hôte. Pour ce faire, procédez comme suit :

1. Ouvrez `config-azure.json` dans Visual Studio Code en exécutant la commande suivante dans une fenêtre de console :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

2. Dans le fichier `config-azure.json`, effectuez les remplacements suivants :

   ![capture d’écran de configuration azure](media/iot-hub-gateway-kit-lessons/lesson3/config_azure.png)

   Remplacez `[IoT hub connection string]` par la chaîne de connexion de l’IoT Hub obtenue.

## <a name="read-messages-from-your-iot-hub"></a>Lire des messages à partir de votre IoT Hub

Si vous avez un TI SensorTag, assurez-vous que vous avez déjà démarré votre SensorTag. Exécutez l’exemple d’application de passerelle et lisez les messages de l'IoT Hub à l'aide de la commande suivante :

```bash
gulp run --iot-hub
```

La commande exécute l’exemple d’application BLE qui lit et regroupe les données de température de votre SensorTag ou de l’appareil simulé et envoie le message à votre IoT Hub toutes les 2 secondes. Elle génère également un processus enfant pour recevoir le message.

Les messages envoyés et reçus sont tous affichés instantanément sur la même fenêtre de console sur l’ordinateur hôte. L’exemple d’instance d’application se ferme automatiquement au bout de 40 secondes.

![Exemple d’application BLE avec des messages envoyés et reçus](media/iot-hub-gateway-kit-lessons/lesson3/gulp_run_read_hub.png)

## <a name="summary"></a>Résumé

Vous avez exécuté un exemple de code pour lire des messages provenant de votre IoT Hub. Vous êtes prêt à lire les messages qui sont stockés dans votre stockage Table Azure.

## <a name="next-steps"></a>Étapes suivantes
[Créer une application de fonction Azure et un compte de stockage Azure](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)





<!--HONumber=Dec16_HO3-->


