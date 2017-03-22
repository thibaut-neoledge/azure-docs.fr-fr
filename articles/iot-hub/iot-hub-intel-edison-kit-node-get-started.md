---
title: "Connecter Intel Edison (Node) à Azure IoT - Prise en main | Microsoft Docs"
description: "Prise en main d’Intel Edison, création de votre Azure IoT Hub et connexion d’Edison à IoT Hub"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "développement intel edison, azure iot hub, guide pratique de l’internet des objets, didacticiel de l’internet des objets, internet des objets adafruit, arduino intel edison, guide pratique d’arduino"
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 7adde91586f5fbbffd0aeaf0efb0810cc891ac0b
ms.openlocfilehash: 43b6efb64c24405ae3638f6d04da5d21e30a55e1
ms.lasthandoff: 03/02/2017


---
# <a name="connect-your-intel-edison-device-to-your-iot-hub-using-nodejs"></a>Connecter votre appareil Intel Edison à votre IoT Hub à l’aide de Node.js
> [!div class="op_single_selector"]
> * [Node.JS](iot-hub-intel-edison-kit-node-get-started.md)
> * [C](iot-hub-intel-edison-kit-c-get-started.md)

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux de l’utilisation d’Intel Edison. Vous apprenez ensuite à connecter en toute transparence vos appareils au cloud avec [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

Vous n’avez pas encore de kit ? Démarrer [ici](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="lesson-1-configure-your-device"></a>Leçon 1 : Configuration de votre appareil
![Diagramme de bout en bout pour la leçon 1](media/iot-hub-intel-edison-lessons/e2e-lesson1.png)

Dans cette leçon, vous configurez votre appareil Intel Edison avec un système d’exploitation, configurez votre environnement de développement et déployez une application sur Edison.

### <a name="configure-your-device"></a>Configurer votre appareil
Configurez Intel Edison pour une première utilisation en assemblant la carte, en la mettant sous tension et en installant l’outil de configuration sur le système d’exploitation de votre ordinateur afin de mettre à jour le micrologiciel de la carte Edison, de définir son mot de passe et de la connecter au réseau Wi-Fi.  

*Durée estimée : 30 minutes*

Accédez à [Configurer votre appareil][configure-your-device].

### <a name="get-the-tools"></a>Obtenir les outils
Téléchargez les outils et les logiciels pour générer et déployer votre première application pour Intel Edison.

*Durée estimée : 20 minutes*

Accédez à [Obtenir les outils][get-the-tools].

### <a name="create-and-deploy-the-blink-application"></a>Créer et déployer l’application blink
Clonez l’exemple d’application de clignotement (blink) à partir de GitHub et utilisez gulp pour déployer cette application sur votre carte Intel Edison. Avec cet exemple d’application, la LED connectée à la carte clignote toutes les deux secondes.

*Durée estimée : 5 minutes*

Accédez à [Créer et déployer l’application Blink][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Leçon 2 : Création de votre IoT Hub
![Diagramme de bout en bout pour la leçon 2](media/iot-hub-intel-edison-lessons/e2e-lesson2.png)

Dans cette leçon, vous créez votre compte Azure gratuit, vous approvisionnez votre Azure IoT Hub et vous créez votre premier appareil dans IoT Hub.

Avant de commencer cette leçon, terminez la Leçon 1.

### <a name="get-the-azure-tools"></a>Obtenir les outils Azure
Installez l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Obtenir les outils Azure][get-azure-tools].

### <a name="create-your-iot-hub-and-register-intel-edison"></a>Créer votre IoT Hub et inscrire Intel Edison
Créez votre groupe de ressources, approvisionnez votre premier Azure IoT Hub et ajoutez votre premier appareil à IoT Hub à l’aide de l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Créer votre IoT Hub et inscrire Intel Edison](iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md).

## <a name="lesson-3-send-device-to-cloud-messages"></a>Leçon 3 : Envoyer des messages appareil-à-cloud
![Diagramme de bout en bout pour la leçon 3](media/iot-hub-intel-edison-lessons/e2e-lesson3.png)

Dans cette leçon, vous envoyez des messages d’Edison à votre IoT Hub. Vous créez également une application de fonction Azure qui récupère les messages entrants à partir de votre IoT Hub et les écrit dans le Stockage Table Azure.

Avant de commencer cette leçon, terminez les leçons 1 et 2.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Création d’une application de fonction Azure et d’un compte de stockage Azure
Utilisez un modèle Azure Resource Manager pour créer une application de fonction Azure et un compte de stockage Azure.

*Durée estimée : 10 minutes*

Accédez à [Création d’une application Azure Function App et d’un compte de stockage Azure][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Exécution d’un exemple d’application pour envoyer des messages appareil-à-cloud
Déployez et exécutez un exemple d’application sur votre appareil Intel Edison qui envoie des messages à IoT Hub.

*Durée estimée : 10 minutes*

Accédez à [Exécution d’un exemple d’application pour envoyer des messages appareil vers cloud][send-device-to-cloud-messages].

### <a name="read-messages-persisted-in-azure-storage"></a>Lecture des messages conservés dans le stockage Azure
Surveillez les messages appareil-à-cloud alors qu’ils sont écrits dans le stockage Azure.

*Durée estimée : 5 minutes*

Accédez à [Lecture des messages conservés dans le stockage Azure][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Leçon 4 : Envoi de messages cloud-à-appareil
![Diagramme de bout en bout pour la leçon 4](media/iot-hub-intel-edison-lessons/e2e-lesson4.png)

Cette leçon vous montre comment envoyer des messages à partir de votre Azure IoT Hub vers Intel Edison. Les messages contrôlent le comportement activé/désactivé de la LED qui est connectée à Edison. Un exemple d’application est préparé pour vous aider à exécuter cette tâche.

Avant de commencer cette leçon, terminez les leçons 1, 2 et 3.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Exécution de l’exemple d’application pour recevoir des messages cloud-à-appareil
L’exemple d’application dans la leçon 4 s’exécute sur Edison et surveille les messages entrants à partir de votre IoT Hub. Une nouvelle tâche gulp envoie des messages à Edison à partir de votre IoT Hub pour faire clignoter la LED.

*Durée estimée : 10 minutes*

Accédez à [Exécution de l’exemple d’application pour recevoir des messages cloud vers appareil][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Section facultative : Modification du comportement activé/désactivé de la LED
Personnalisez les messages pour modifier le comportement activé/désactivé de la LED.

*Durée estimée : 10 minutes*

Accédez à [Section facultative : Modification du comportement activé/désactivé de la LED][change-the-on-and-off-behavior-of-the-led].

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous rencontrez des problèmes au cours des leçons, recherchez des solutions dans l’article [Résolution des problèmes][troubleshooting].
<!-- Images and links -->

[configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-node-lesson1-deploy-blink-app.md
[get-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-intel-edison-kit-node-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-node-lesson3-read-table-storage.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-node-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
