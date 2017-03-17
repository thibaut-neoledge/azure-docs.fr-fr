---
title: "Connecter Raspberry Pi (C) à Azure IoT - Leçon 1 : Prise en main | Microsoft Docs"
description: "Bien démarrer avec Raspberry Pi 3, création de votre Azure IoT Hub et connexion de Pi à IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, bien démarrer avec l’internet des objets, kit d’outils iot"
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: c74e8c40ba259df8d22c11f930073ab83e900c62
ms.lasthandoff: 03/02/2017


---
# <a name="connect-your-raspberry-pi-3-device-to-your-iot-hub-using-c"></a>Connecter votre appareil Raspberry Pi 3 à votre IoT hub à l’aide de C
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-raspberry-pi-kit-node-get-started.md)
> * [C](iot-hub-raspberry-pi-kit-c-get-started.md)

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux de l’utilisation de Raspberry Pi 3 exécutant Raspbian. Vous apprenez ensuite à connecter en toute transparence vos appareils au cloud avec [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Pour obtenir des exemples Windows 10 IoT Standard, accédez au [centre de développement Windows](http://www.windowsondevices.com/).

## <a name="lesson-1-configure-your-device"></a>Leçon 1 : Configuration de votre appareil
![Diagramme de bout en bout pour la leçon 1](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

Dans cette leçon, vous configurez votre appareil Raspberry Pi 3 avec un système d’exploitation, vous configurez votre environnement de développement et déployez une application sur Pi.

### <a name="configure-your-device"></a>Configurer votre appareil
Configurez Raspberry Pi 3 pour une première utilisation et installez Raspbian. Raspbian est un système d’exploitation gratuit optimisé pour le matériel Raspberry Pi.

*Durée estimée : 30 minutes*

Accédez à [Configurer votre appareil](iot-hub-raspberry-pi-kit-c-lesson1-configure-your-device.md).

### <a name="get-the-tools"></a>Obtenir les outils
Téléchargez les outils et les logiciels pour générer et déployer votre première application pour Raspberry Pi 3.

*Durée estimée : 20 minutes*

Accédez à [Obtenir les outils](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md).

### <a name="create-and-deploy-the-blink-application"></a>Créer et déployer l’application blink
Clonez l’exemple d’application de clignotement (blink) C à partir de GitHub et utilisez gulp pour déployer cette application sur votre carte Raspberry Pi 3. Avec cet exemple d’application, la LED connectée à la carte clignote toutes les deux secondes.

*Durée estimée : 5 minutes*

Accédez à [Créer et déployer l’application de clignotement (blink)](iot-hub-raspberry-pi-kit-c-lesson1-deploy-blink-app.md).

## <a name="lesson-2-create-your-iot-hub"></a>Leçon 2 : Création de votre IoT Hub
![Diagramme de bout en bout pour la leçon 2](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

Dans cette leçon, vous créez votre compte Azure gratuit, vous approvisionnez votre Azure IoT Hub et vous créez votre premier appareil dans IoT Hub.

Avant de commencer cette leçon, terminez la Leçon 1.

### <a name="get-the-azure-tools"></a>Obtenir les outils Azure
Installez l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Obtenir les outils Azure](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md).

### <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>Création de votre IoT Hub et inscription de Raspberry Pi 3
Créez votre groupe de ressources, approvisionnez votre premier Azure IoT Hub et ajoutez votre premier appareil à IoT Hub à l’aide de l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Création de votre IoT Hub et inscription de Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md).

## <a name="lesson-3-send-device-to-cloud-messages"></a>Leçon 3 : Envoyer des messages appareil-à-cloud
![Diagramme de bout en bout pour la leçon 3](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

Dans cette leçon, vous envoyez des messages à partir de Pi à votre IoT Hub. Vous créez également une application de fonction Azure qui récupère les messages entrants à partir de votre IoT Hub et les écrit dans le Stockage Table Azure.

Avant de commencer cette leçon, terminez les leçons 1 et 2.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Création d’une application de fonction Azure et d’un compte de stockage Azure
Utilisez un modèle Azure Resource Manager pour créer une application de fonction Azure et un compte de stockage Azure.

*Durée estimée : 10 minutes*

Accédez à [Création d’une application de fonction Azure et d’un compte de stockage Azure](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md).

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Exécution d’un exemple d’application pour envoyer des messages appareil-à-cloud
Déployez et exécutez un exemple d’application sur votre appareil Raspberry Pi 3 qui envoie des messages à IoT Hub.

*Durée estimée : 10 minutes*

Accédez à [Exécution d’un exemple d’application pour envoyer des messages appareil-à-cloud](iot-hub-raspberry-pi-kit-c-lesson3-run-azure-blink.md).

### <a name="read-messages-persisted-in-azure-storage"></a>Lecture des messages conservés dans le stockage Azure
Surveillez les messages appareil-à-cloud alors qu’ils sont écrits dans le stockage Azure.

*Durée estimée : 5 minutes*

Accédez à [Lecture des messages conservés dans le stockage Azure](iot-hub-raspberry-pi-kit-c-lesson3-read-table-storage.md).

## <a name="lesson-4-send-cloud-to-device-messages"></a>Leçon 4 : Envoi de messages cloud-à-appareil
![Diagramme de bout en bout pour la leçon 4](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

Cette leçon vous montre comment envoyer des messages à partir de votre Azure IoT Hub vers Raspberry Pi 3. Les messages contrôlent le comportement activé/désactivé de la LED qui est connectée à Pi. Un exemple d’application est préparé pour vous aider à exécuter cette tâche.

Avant de commencer cette leçon, terminez les leçons 1, 2 et 3.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Exécution de l’exemple d’application pour recevoir des messages cloud-à-appareil
L’exemple d’application dans la leçon 4 s’exécute sur Pi et surveille les messages entrants à partir de votre IoT Hub. Une nouvelle tâche gulp envoie des messages à Pi à partir de votre IoT Hub pour faire clignoter la LED.

*Durée estimée : 10 minutes*

Accédez à [Exécution de l’exemple d’application pour recevoir des messages cloud-à-appareil](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md).

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Section facultative : Modification du comportement activé/désactivé de la LED
Personnalisez les messages pour modifier le comportement activé/désactivé de la LED.

*Durée estimée : 10 minutes*

Accédez à [Section facultative : Modification du comportement activé/désactivé de la LED](iot-hub-raspberry-pi-kit-c-lesson4-change-led-behavior.md).

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous rencontrez des problèmes au cours des leçons, recherchez des solutions dans l’article [Résolution des problèmes](iot-hub-raspberry-pi-kit-c-troubleshooting.md).

