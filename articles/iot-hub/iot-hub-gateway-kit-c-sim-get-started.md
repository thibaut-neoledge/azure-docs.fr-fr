---
title: "Appareil simulé et passerelle Azure IoT - Prise en main | Microsoft Docs"
description: "Prise en main du Kit de démarrage de passerelle IoT, créer votre Azure IoT Hub et connecter la passerelle à l&quot;IoT Hub"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, passerelle iot, bien démarrer avec l’internet des objets, kit d’outils iot"
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 06161c2950fbe99d2e4f9c1b416695a5f9f18ca4
ms.lasthandoff: 01/25/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-simulated-device"></a>Prise en main du Kit de démarrage de la passerelle IoT avec un appareil simulé

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Appareil simulé](iot-hub-gateway-kit-c-sim-get-started.md)

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux du [Kit de démarrage de la passerelle IoT](https://aka.ms/gateway-kit). Vous allez travailler avec Intel NUC exécutant Wind River Linux. Vous apprendrez ensuite à connecter en toute transparence vos appareils au cloud à l’aide d’Azure IoT Hub.

***
**Vous n’avez pas encore de kit ?** Cliquez [ici](https://aka.ms/gateway-kit).
***

## <a name="lesson-1-configure-your-nuc"></a>Leçon 1 : Configuration de votre NUC
![Diagramme de bout en bout pour la leçon&1;](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

Dans cette leçon, vous configurez des NUC Intel (Next Unit of Computing) dans le Kit en tant que passerelle Azure IoT, installez le package du Kit de développement logiciel (SDK) Gateway Azure IoT sur la NUC et exécutez un exemple d’application pour vérifier la fonctionnalité de passerelle.

*Durée estimée : 15 minutes*

Consultez [Configurer Intel NUC comme passerelle IoT](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md)

## <a name="lesson-2-create-your-iot-hub"></a>Leçon 2 : Création de votre IoT Hub
![Diagramme de bout en bout pour la leçon&2;](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

Dans cette leçon, vous installez les outils et logiciels sur votre ordinateur hôte. Vous créez ensuite votre compte Azure gratuit, vous approvisionnez votre Azure IoT Hub et vous créez votre premier appareil dans l’IoT Hub.

Avant de commencer cette leçon, terminez la Leçon 1.

### <a name="get-the-tools"></a>Obtenir les outils
Installez les outils et logiciels sur votre ordinateur hôte.

*Durée estimée : 20 minutes*

Accédez à [Obtenir les outils](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md).

### <a name="create-an-iot-hub-and-register-your-device"></a>Créer un IoT hub et enregistrer votre appareil
Créez votre groupe de ressources, approvisionnez votre premier Azure IoT Hub et ajoutez votre premier appareil à l’IoT Hub à l’aide de l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Créer un IoT Hub et enregistrer votre appareil](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub"></a>Leçon 3 : Recevoir des messages à partir de l’appareil simulé et lire des messages à partir de votre IoT Hub
Dans cette leçon, vous utiliserez des scripts pour automatiser la configuration et l’exécution d’une application d'appareil simulé dans votre passerelle. L’application d'appareil simulé génère des exemples de données de température et les envoie à un module IoT Hub. Le module IoT Hub regroupe les données reçues et les envoie à votre IoT Hub via l’infrastructure de passerelle fournie dans le Kit de développement logiciel Gateway Azure IoT.

![Diagramme de bout en bout pour la leçon 3](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

### <a name="configure-and-run-a-simulated-device"></a>Configurer et exécuter un appareil simulé
Préparez les exemples de codes. Puis configurez et exécutez l'exemple d’application d’appareil simulé.

*Durée estimée : 15 minutes*

Accédez à [Configurer et exécuter un appareil simulé](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)

### <a name="read-messages-from-your-iot-hub"></a>Lire des messages à partir de votre IoT Hub
Exécutez un exemple de code sur votre ordinateur hôte pour lire les messages à partir de votre IoT Hub.

*Durée estimée : 15 minutes*

Accédez à [Lire des messages à partir de votre IoT Hub](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>Leçon 4 : Enregistrer des messages sur le stockage Table Azure
Créez une application de fonction Azure qui récupère les messages entrants à partir de votre IoT Hub et les écrit dans le stockage Table Azure.

![Diagramme de bout en bout pour la leçon 4](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Création d’une application de fonction Azure et d’un compte de stockage Azure
Utilisez un modèle Azure Resource Manager pour créer une application de fonction Azure et un compte de stockage Azure.

*Durée estimée : 10 minutes*

Accédez à [Création d’une application de fonction Azure et d’un compte de stockage Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)

### <a name="read-messages-persisted-in-azure-table-storage"></a>Lire des messages conservés dans le stockage Table Azure
Surveillez les messages passerelle-à-cloud lorsqu’ils sont écrits dans le stockage Table Azure.

*Durée estimée : 5 minutes*

Accédez à [Lire des messages conservés dans le stockage Table Azure](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md).

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous rencontrez des problèmes au cours des leçons, recherchez des solutions dans l’article [Résolution des problèmes](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="explore-more"></a>Aller plus loin
Consultez la [zone pour développeurs Intel IoT Gateway Kit](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit) pour en savoir plus.
