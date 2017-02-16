---
title: "Enregistrer des messages dans le cloud Azure et les lire à partir du cloud | Microsoft Docs"
description: "Enregistrez des messages à partir de l’Intel NUC dans votre IoT Hub, écrivez-les dans le stockage Table Azure, puis lisez-les à partir du cloud."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "stockage de données dans le cloud, les données stockées dans le cloud, service cloud iot"
ms.assetid: f84f9a85-e2c4-4a92-8969-f65eb34c194e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: 73c5268ece75776a3c5007b612f84bd29cc7784d


---
# <a name="create-an-azure-function-app-and-storage-account"></a>Créer une application de fonction Azure et un compte de stockage

Azure Functions est une solution conçue pour exécuter facilement des petits morceaux de code (_fonctions_) dans le cloud. Une application de fonction Azure héberge l’exécution de vos fonctions dans Azure. 

## <a name="what-you-will-do"></a>Procédure à suivre

- Utilisez un modèle Azure Resource Manager pour créer une application de fonction Azure et un compte de Stockage Azure. L’application de fonction Azure écoute les événements d’Azure IoT Hub, traite les messages entrants et les écrit dans le stockage Table Azure.

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).


## <a name="what-you-will-learn"></a>Contenu

Dans cette leçon, vous allez apprendre :

- l’utilisation d’Azure Resource Manager pour déployer des ressources Azure ;
- l’utilisation d’une application de fonction Azure pour traiter les messages de l’IoT Hub et les écrire dans le stockage Table Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin

Vous devez avoir terminé les leçons précédentes :

- [Leçon 1 : Configurer l’Intel NUC comme passerelle IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
- [Leçon 2 : Préparer votre ordinateur hôte et Azure IoT Hub](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)
- [Leçon 3 : Recevoir des messages à partir de SensorTag et lire des messages à partir de votre IoT Hub](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

## <a name="open-a-sample-app"></a>Ouvrir un exemple d’application

Accédez à votre dossier de référentiel `iot-hub-c-intel-nuc-gateway-getting-started`, initialisez les fichiers de configuration, puis ouvrez l’exemple de projet dans Visual Studio Code en exécutant la commande suivante :

```bash
cd Lesson4
npm install
gulp init
code .
```

![structure du référentiel](media/iot-hub-gateway-kit-lessons/lesson4/arm_template.png)

- Le fichier `arm-template.json` est le modèle Azure Resource Manager qui contient une application de fonction Azure et un compte de Stockage Azure.
- Le fichier `arm-template-param.json` est le fichier de configuration utilisé par le modèle Azure Resource Manager.
- Le sous-dossier `ReceiveDeviceMessages` contient le code Node.js pour la fonction Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurer des modèles Azure Resource Manager et créer des ressources dans Azure

Mettez à jour le fichier `arm-template-param.json` dans Visual Studio Code.

![modèle arm json](media/iot-hub-gateway-kit-lessons/lesson4/arm_template_param.png)

- Remplacez `[your IoT Hub name]` par `{my hub name}` que vous avez spécifié à la leçon 2.

Une fois que vous avez mis à jour le fichier `arm-template-param.json`, déployez les ressources dans Azure en exécutant la commande suivante :

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-gateway
```

Si vous n’avez pas modifié la valeur à la leçon 2, utilisez `iot-gateway` en tant que valeur de `{resource group name}`.

## <a name="summary"></a>Résumé

Vous avez créé votre application de fonction Azure pour traiter les messages de l’IoT Hub et un compte de Stockage Azure pour stocker ces messages. Vous pouvez maintenant lire les messages envoyés par votre passerelle à votre IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Lire des messages conservés dans le stockage Azure](iot-hub-gateway-kit-c-lesson4-read-table-storage.md).



<!--HONumber=Dec16_HO3-->


