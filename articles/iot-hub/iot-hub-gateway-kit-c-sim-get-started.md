---
title: "Appareil simulé et passerelle Azure IoT - Prise en main | Microsoft Docs"
description: "Prise en main du Kit de démarrage de passerelle IoT, créer votre Azure IoT Hub et connecter la passerelle à l'IoT Hub"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot hub, passerelle iot, bien démarrer avec l’internet des objets, kit d’outils iot"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 916fa40d9ac857dfa72197b40c232834593d3891
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---

<a id="get-started-with-iot-gateway-starter-kit-with-a-simulated-device" class="xliff"></a>

# Prise en main du Kit de démarrage de la passerelle IoT avec un appareil simulé

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Appareil simulé](iot-hub-gateway-kit-c-sim-get-started.md)

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux du [Kit de démarrage de la passerelle IoT](https://aka.ms/gateway-kit). Vous allez travailler avec Intel NUC exécutant Wind River Linux. Vous apprendrez ensuite à connecter en toute transparence vos appareils au cloud à l’aide d’Azure IoT Hub.

***
**Vous n’avez pas encore de kit ?** Cliquez [ici](https://aka.ms/gateway-kit).
***

<a id="lesson-1-configure-your-nuc" class="xliff"></a>

## Leçon 1 : Configuration de votre NUC
![Diagramme de bout en bout pour la leçon 1](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

Dans cette leçon, vous allez configurer des NUC Intel (Next Unit of Computing) dans le Kit en tant que passerelle Azure IoT, installer le package Azure IoT Edge sur les NUC et exécuter un exemple d’application pour vérifier la fonctionnalité de passerelle.

*Durée estimée : 15 minutes*

Consultez [Configurer Intel NUC comme passerelle IoT](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md)

<a id="lesson-2-create-your-iot-hub" class="xliff"></a>

## Leçon 2 : Création de votre IoT Hub
![Diagramme de bout en bout pour la leçon 2](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

Dans cette leçon, vous installez les outils et logiciels sur votre ordinateur hôte. Vous créez ensuite votre compte Azure gratuit, vous approvisionnez votre Azure IoT Hub et vous créez votre premier appareil dans l’IoT Hub.

Avant de commencer cette leçon, terminez la Leçon 1.

<a id="get-the-tools" class="xliff"></a>

### Obtenir les outils
Installez les outils et logiciels sur votre ordinateur hôte.

*Durée estimée : 20 minutes*

Accédez à [Obtenir les outils](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md).

<a id="create-an-iot-hub-and-register-your-device" class="xliff"></a>

### Créer un IoT hub et enregistrer votre appareil
Créez votre groupe de ressources, approvisionnez votre premier Azure IoT Hub et ajoutez votre premier appareil à l’IoT Hub à l’aide de l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Créer un IoT Hub et enregistrer votre appareil](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

<a id="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub" class="xliff"></a>

## Leçon 3 : Recevoir des messages à partir de l’appareil simulé et lire des messages à partir de votre IoT Hub
Dans cette leçon, vous utiliserez des scripts pour automatiser la configuration et l’exécution d’une application d'appareil simulé dans votre passerelle. L’application d'appareil simulé génère des exemples de données de température et les envoie à un module IoT Hub. Le module IoT Hub regroupe les données reçues et les envoie à votre IoT Hub via l’infrastructure de passerelle fournie dans Azure IoT Edge.

![Diagramme de bout en bout pour la leçon 3](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

<a id="configure-and-run-a-simulated-device" class="xliff"></a>

### Configurer et exécuter un appareil simulé
Préparez les exemples de codes. Puis configurez et exécutez l'exemple d’application d’appareil simulé.

*Durée estimée : 15 minutes*

Accédez à [Configurer et exécuter un appareil simulé](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)

<a id="read-messages-from-your-iot-hub" class="xliff"></a>

### Lire des messages à partir de votre IoT Hub
Exécutez un exemple de code sur votre ordinateur hôte pour lire les messages à partir de votre IoT Hub.

*Durée estimée : 15 minutes*

Accédez à [Lire des messages à partir de votre IoT Hub](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)

<a id="lesson-4-save-messages-to-azure-table-storage" class="xliff"></a>

## Leçon 4 : Enregistrer des messages sur le stockage Table Azure
Créez une application de fonction Azure qui récupère les messages entrants à partir de votre IoT Hub et les écrit dans le stockage Table Azure.

![Diagramme de bout en bout pour la leçon 4](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

<a id="create-an-azure-function-app-and-azure-storage-account" class="xliff"></a>

### Création d’une application de fonction Azure et d’un compte de stockage Azure
Utilisez un modèle Azure Resource Manager pour créer une application de fonction Azure et un compte de stockage Azure.

*Durée estimée : 10 minutes*

Accédez à [Création d’une application de fonction Azure et d’un compte de stockage Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)

<a id="read-messages-persisted-in-azure-table-storage" class="xliff"></a>

### Lire des messages conservés dans le stockage Table Azure
Surveillez les messages passerelle-à-cloud lorsqu’ils sont écrits dans le stockage Table Azure.

*Durée estimée : 5 minutes*

Accédez à [Lire des messages conservés dans le stockage Table Azure](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md).

<a id="troubleshooting" class="xliff"></a>

## Résolution de problèmes
Si vous rencontrez des problèmes au cours des leçons, recherchez des solutions dans l’article [Résolution des problèmes](iot-hub-gateway-kit-c-sim-troubleshooting.md).

<a id="explore-more" class="xliff"></a>

## Aller plus loin
Consultez la [zone pour développeurs Intel IoT Gateway Kit](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit) pour en savoir plus.
