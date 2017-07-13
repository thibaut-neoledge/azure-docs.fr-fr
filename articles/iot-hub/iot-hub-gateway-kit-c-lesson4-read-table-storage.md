---
title: "Appareil SensorTag et passerelle Azure IoT - Leçon 4 : Stockage de table | Microsoft Docs"
description: "Enregistrez des messages à partir d’Intel NUC dans votre hub IoT, écrivez-les dans le stockage Table Azure, puis lisez-les à partir du cloud."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "récupérer des données du cloud, service cloud iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 8ca78045-ad92-4a6a-90f1-05f9668e6f0e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 293343e5db58bebc8c2e27d54d6396212fe6d3a1
ms.contentlocale: fr-fr
ms.lasthandoff: 01/25/2017

---

<a id="read-messages-persisted-in-azure-table-storage" class="xliff"></a>

# Lire des messages conservés dans le stockage Table Azure

<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre

- Exécutez l’exemple d’application de votre passerelle qui envoie des messages à votre IoT Hub.
- Exécutez ensuite l’exemple de code sur votre ordinateur hôte pour lire les messages dans votre stockage Table Azure. 

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

<a id="what-you-will-learn" class="xliff"></a>

## Contenu

Utilisation de l’outil gulp pour exécuter l’exemple de code permettant de lire les messages dans votre stockage Table Azure.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin

Vous avez effectué les tâches suivantes :

- [créer l’application de fonction Azure et le compte de stockage Azure](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md) ;
- [exécuter l’exemple d’application de la passerelle](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md) ;
- [lire des messages à partir de votre IoT Hub](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md).

<a id="get-your-azure-storage-connection-strings" class="xliff"></a>

## Obtenir vos chaînes de connexion de stockage Azure

Au début de cette leçon, vous avez créé un compte de stockage Azure. Pour obtenir la chaîne de connexion du compte de stockage Azure, exécutez les commandes suivantes :

* Répertoriez tous vos comptes de stockage.

```bash
az storage account list -g iot-gateway --query [].name
```

* Obtenez la chaîne de connexion de stockage Azure.

```bash
az storage account show-connection-string -g iot-gateway -n {storage name}
```

Si vous n’avez pas modifié la valeur à la leçon 2, utilisez iot-gateway en tant que valeur de `{resource group name}`.

<a id="configure-the-device-connection" class="xliff"></a>

## Configuration de la connexion de l’appareil

Mettez à jour le fichier `config-azure.json` afin que l’exemple de code qui s’exécute sur l’ordinateur hôte puisse lire le message dans votre stockage Table Azure. Procédez comme suit pour configurer la connexion de l’appareil :

1. Ouvrez le fichier de configuration d’appareil `config-azure.json` en exécutant les commandes suivantes :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-azure.json
   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-azure.json
   ```

   ![configuration](media/iot-hub-gateway-kit-lessons/lesson4/config_azure.png)

2. Remplacez `[Azure storage connection string]` par la chaîne de connexion de stockage Azure que vous avez obtenue.

   `[IoT hub connection string]` doit déjà être remplacé dans la section [Lire des messages à partir d’Azure IoT Hub](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md) de la leçon&3;.

<a id="read-messages-in-your-azure-table-storage" class="xliff"></a>

## Lire des messages dans votre stockage Table Azure

Exécutez l’exemple d’application de la passerelle et lisez les messages de stockage Table Azure avec la commande suivante :

```bash
gulp run --table-storage
```

Votre IoT Hub déclenche votre application Azure Function pour enregistrer un nouveau message arrivant dans votre stockage Table Azure.
La commande `gulp run` exécute l’exemple d’application de la passerelle qui envoie des messages à votre IoT Hub. Avec le paramètre `table-storage`, elle génère également un processus enfant pour recevoir le message enregistré dans votre stockage Table Azure.

Les messages envoyés et reçus sont tous affichés instantanément sur la même fenêtre de console sur l’ordinateur hôte. L’exemple d’instance d’application se ferme automatiquement au bout de 40 secondes.

   ![lecture de gulp](media/iot-hub-gateway-kit-lessons/lesson4/gulp_run_read_table.png)


<a id="summary" class="xliff"></a>

## Résumé

Vous avez exécuté l’exemple de code pour lire les messages de votre stockage Table Azure enregistrés par votre application Azure Function.
