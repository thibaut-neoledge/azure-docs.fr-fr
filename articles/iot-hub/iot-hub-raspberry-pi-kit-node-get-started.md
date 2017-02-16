---
title: "Connecter un Raspberry Pi 3 à Azure IoT Hub | Microsoft Docs"
description: "Prise en main d’un appareil Raspberry Pi 3, création de votre Azure IoT Hub et connexion de votre Pi à IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot hub, bien démarrer avec l’internet des objets, kit d’outils iot"
experimental: true
experiment_id: xshi-happypathemu-20161202
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d04f9d219451d683b572df43f73944011d686797


---
# <a name="get-started-with-raspberry-pi-3-nodejs"></a>Prise en main de Raspberry Pi 3 (Node.js)
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-raspberry-pi-kit-node-get-started.md)
> * [C](iot-hub-raspberry-pi-kit-c-get-started.md)

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux de l’utilisation de Raspberry Pi 3 exécutant Raspbian. Vous apprenez ensuite à connecter en toute transparence vos appareils au cloud avec [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Pour obtenir des exemples Windows 10 IoT Standard, accédez au [centre de développement Windows](http://www.windowsondevices.com/).

Vous n’avez pas encore de kit ? Commencez [ici](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="lesson-1-configure-your-device"></a>Leçon 1 : Configuration de votre appareil
![Diagramme de bout en bout pour la leçon 1](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

Dans cette leçon, vous configurez votre appareil Raspberry Pi 3 avec un système d’exploitation, vous configurez votre environnement de développement et déployez une application sur Pi.

### <a name="configure-your-device"></a>Configurer votre appareil
Configurez Raspberry Pi 3 pour une première utilisation et installez Raspbian. Raspbian est un système d’exploitation gratuit optimisé pour le matériel Raspberry Pi.

*Durée estimée : 30 minutes*

Accédez à [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md).

### <a name="get-the-tools"></a>Obtenir les outils
Téléchargez les outils et les logiciels pour générer et déployer votre première application pour Raspberry Pi 3.

*Durée estimée : 20 minutes*

Accédez à [Obtenir les outils](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md).

### <a name="create-and-deploy-the-blink-application"></a>Créer et déployer l’application blink
Clonez l’exemple d’application de clignotement (blink) Node.js à partir de GitHub et utilisez gulp pour déployer cette application sur votre carte Raspberry Pi 3. Avec cet exemple d’application, la LED connectée à la carte clignote toutes les deux secondes.

*Durée estimée : 5 minutes*

Accédez à [Créer et déployer l’application de clignotement (blink)](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md).

## <a name="lesson-2-create-your-iot-hub"></a>Leçon 2 : Création de votre IoT Hub
![Diagramme de bout en bout pour la leçon 2](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

Dans cette leçon, vous créez votre compte Azure gratuit, vous approvisionnez votre Azure IoT Hub et vous créez votre premier appareil dans IoT Hub.

Avant de commencer cette leçon, terminez la Leçon 1.

### <a name="get-the-azure-tools"></a>Obtenir les outils Azure
Installez l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Obtenir les outils Azure](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md).

### <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>Création de votre IoT Hub et inscription de Raspberry Pi 3
Créez votre groupe de ressources, approvisionnez votre premier Azure IoT Hub et ajoutez votre premier appareil à IoT Hub à l’aide de l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Création de votre IoT Hub et inscription de Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).

## <a name="lesson-3-send-device-to-cloud-messages"></a>Leçon 3 : Envoyer des messages appareil-à-cloud
![Diagramme de bout en bout pour la leçon 3](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

Dans cette leçon, vous envoyez des messages à partir de Pi à votre IoT Hub. Vous créez également une application de fonction Azure qui récupère les messages entrants à partir de votre IoT Hub et les écrit dans le Stockage Table Azure.

Avant de commencer cette leçon, terminez la leçon 1 et la leçon 2.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Création d’une application de fonction Azure et d’un compte de stockage Azure
Utilisez un modèle Azure Resource Manager pour créer une application de fonction Azure et un compte de stockage Azure.

*Durée estimée : 10 minutes*

Accédez à [Création d’une application de fonction Azure et d’un compte de stockage Azure](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Exécution d’un exemple d’application pour envoyer des messages appareil-à-cloud
Déployez et exécutez un exemple d’application sur votre appareil Raspberry Pi 3 qui envoie des messages à IoT Hub.

*Durée estimée : 10 minutes*

Accédez à [Exécution d’un exemple d’application pour envoyer des messages appareil-à-cloud](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md).

### <a name="read-messages-persisted-in-azure-storage"></a>Lecture des messages conservés dans le stockage Azure
Surveillez les messages appareil-à-cloud alors qu’ils sont écrits dans le stockage Azure.

*Durée estimée : 5 minutes*

Accédez à [Lecture des messages conservés dans le stockage Azure](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md).

## <a name="lesson-4-send-cloud-to-device-messages"></a>Leçon 4 : Envoi de messages cloud-à-appareil
![Diagramme de bout en bout pour la leçon 4](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

Cette leçon vous montre comment envoyer des messages à partir de votre Azure IoT Hub vers Raspberry Pi 3. Les messages contrôlent le comportement activé/désactivé de la LED qui est connectée à Pi. Un exemple d’application est préparé pour vous aider à exécuter cette tâche.

Avant de commencer cette leçon, terminez la leçon 1, la leçon 2 et la leçon 3.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Exécution de l’exemple d’application pour recevoir des messages cloud-à-appareil
L’exemple d’application dans la leçon 4 s’exécute sur Pi et surveille les messages entrants à partir de votre IoT Hub. Une nouvelle tâche gulp envoie des messages à Pi à partir de votre IoT Hub pour faire clignoter la LED.

*Durée estimée : 10 minutes*

Accédez à [Exécution de l’exemple d’application pour recevoir des messages cloud-à-appareil](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Section facultative : Modification du comportement activé/désactivé de la LED
Personnalisez les messages pour modifier le comportement activé/désactivé de la LED.

*Durée estimée : 10 minutes*

Accédez à [Section facultative : Modification du comportement activé/désactivé de la LED](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md).

## <a name="troubleshooting"></a>Résolution des problèmes
Si vous rencontrez des problèmes au cours des leçons, vous pouvez rechercher des solutions dans l’article [Résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).




<!--HONumber=Dec16_HO2-->


