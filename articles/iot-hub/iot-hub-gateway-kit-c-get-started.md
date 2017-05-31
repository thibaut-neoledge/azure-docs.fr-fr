---
title: Appareil SensorTag et passerelle Azure IoT - Prise en main | Microsoft Docs
description: "Prise en main du Kit de démarrage de passerelle IoT, créer votre Azure IoT hub et connecter SensorTag et la passerelle vers l’IoT hub"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, passerelle iot, bien démarrer avec l’internet des objets, kit d’outils iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 56d05f4e-f2c1-4b22-8701-f01e14deead6
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 624bdc7877d5048da08897f868272fd8e8f3f7b6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-sensortag"></a>Prise en main du Kit de démarrage de la passerelle IoT avec un SensorTag

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Appareil simulé](iot-hub-gateway-kit-c-sim-get-started.md)

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux du [Kit de démarrage de la passerelle IoT](https://aka.ms/gateway-kit). Vous allez travailler avec Intel NUC exécutant Wind River Linux et [TI SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html#main). Vous apprendrez ensuite à connecter en toute transparence vos appareils au cloud à l’aide d’Azure IoT Hub.

***
**Vous n’avez pas encore de kit ?** Cliquez [ici](https://aka.ms/gateway-kit). **Vous n’avez pas de SensorTag ?** [Commencez avec un appareil simulé](iot-hub-gateway-kit-c-sim-get-started.md) ou [achetez un SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/?INTC=SensorTag&HQS=sensortag)
***

## <a name="lesson-1-configure-your-nuc"></a>Leçon 1 : Configuration de votre NUC
![Diagramme de bout en bout pour la leçon 1](media/iot-hub-gateway-kit-lessons/e2e-lesson1.png)

Dans cette leçon, vous allez configurer des NUC Intel (Next Unit of Computing) dans le Kit en tant que passerelle Azure IoT, installer le package Azure IoT Edge sur les NUC et exécuter un exemple d’application pour vérifier la fonctionnalité de passerelle.

*Durée estimée : 15 minutes*

Consultez [Configurer Intel NUC comme passerelle IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)

## <a name="lesson-2-create-your-iot-hub"></a>Leçon 2 : Création de votre IoT Hub
![Diagramme de bout en bout pour la leçon 2](media/iot-hub-gateway-kit-lessons/e2e-lesson2.png)

Dans cette leçon, vous installez les outils et logiciels sur votre ordinateur hôte. Vous créez ensuite votre compte Azure gratuit, vous approvisionnez votre Azure IoT Hub et vous créez votre premier appareil dans l’IoT Hub.

Avant de commencer cette leçon, terminez la Leçon 1.

### <a name="get-the-tools"></a>Obtenir les outils
Installez les outils et logiciels sur votre ordinateur hôte.

*Durée estimée : 20 minutes*

Accédez à [Obtenir les outils](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md).

### <a name="create-an-iot-hub-and-register-your-device"></a>Créer un IoT hub et enregistrer votre appareil
Créez votre groupe de ressources, approvisionnez votre premier Azure IoT Hub et ajoutez votre premier appareil à l’IoT Hub à l’aide de l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Créer un IoT hub et enregistrer votre appareil](iot-hub-gateway-kit-c-lesson2-register-device.md)

## <a name="lesson-3-receive-messages-from-sensortag-and-read-messages-from-your-iot-hub"></a>Leçon 3 : Recevoir des messages à partir de SensorTag et lire des messages à partir de votre IoT Hub
Dans cette leçon, vous utiliserez des scripts pour automatiser la configuration et l’exécution d’un exemple d’application BLE dans votre passerelle. De telles applications utilisent une collection de modules pour agréger et transformer des données, traiter des commandes ou effectuer des tâches associées. Les modules communiquent entre eux par le bais d’un courtier de messages. L’exemple d’application comprend un module BLE et un module IoT hub. Le module BLE reçoit des données du SensorTag BLE. Le module IoT Hub regroupe les données reçues et les envoie à votre IoT Hub via l’infrastructure de passerelle fournie dans Azure IoT Edge.

![Diagramme de bout en bout pour la leçon 3](media/iot-hub-gateway-kit-lessons/e2e-lesson3.png)

### <a name="configure-and-run-the-ble-sample-app"></a>Configurer et exécuter l’exemple d’application BLE
Configurez la connectivité entre SensorTag et votre passerelle. Terminez la configuration, puis exécutez l’application d’exemple BLE.

*Durée estimée : 15 minutes*

Accédez à [Configurer et exécuter l’exemple d’application BLE](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

### <a name="read-messages-from-your-iot-hub"></a>Lire des messages à partir de votre IoT hub
Exécutez l’exemple de code sur votre ordinateur hôte pour lire les messages provenant de votre IoT hub.

*Durée estimée : 15 minutes*

Accédez à [Lire des messages à partir de votre IoT Hub](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>Leçon 4 : Enregistrer des messages sur le stockage Table Azure
Créez une application de fonction Azure qui récupère les messages entrants à partir de votre IoT Hub et les écrit dans le stockage Table Azure.

![Diagramme de bout en bout pour la leçon 4](media/iot-hub-gateway-kit-lessons/e2e-lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Création d’une application de fonction Azure et d’un compte de stockage Azure
Utilisez un modèle Azure Resource Manager pour créer une application de fonction Azure et un compte de stockage Azure.

*Durée estimée : 10 minutes*

Accédez à [Création d’une application de fonction Azure et d’un compte de stockage Azure](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)

### <a name="read-messages-persisted-in-azure-table-storage"></a>Lire des messages conservés dans le stockage Table Azure
Surveillez les messages passerelle-à-cloud lorsqu’ils sont écrits dans le stockage Table Azure.

*Durée estimée : 5 minutes*

Accédez à [Lire des messages conservés dans le stockage Table Azure](iot-hub-gateway-kit-c-lesson4-read-table-storage.md).

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous rencontrez des problèmes au cours des leçons, recherchez des solutions dans l’article [Résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="explore-more"></a>Aller plus loin
Consultez la [zone pour développeurs Intel IoT Gateway Kit](http://software.intel.com/iot/microsoft-azure) pour en savoir plus.
