---
title: Guide pratique du starter kit Adafruit Feather M0 WiFi Azure IoT | Microsoft Docs
description: "Apprenez à utiliser Adafruit Feather M0 WiFi, créez votre IoT Hub Azure et connectez Adafruit Feather M0 WiFi à l’IoT Hub"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, guide pratique de l’internet des objets, didacticiel de l’internet des objets, internet des objets adafruit, guide pratique d’arduino"
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 8689ee35e880b1aa774b09bb194b43f280b2af79


---
# <a name="get-started-with-your-arduino-board-adafruit-feather-m0-wifi"></a>Guide pratique de votre carte Arduino : Adafruit Feather M0 WiFi

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux de l’utilisation de votre carte Arduino. Vous apprenez ensuite à connecter en toute transparence vos appareils au cloud avec [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

## <a name="lesson-1-configure-your-device"></a>Leçon 1 : Configuration de votre appareil
![Diagramme de bout en bout pour la leçon 1][Lesson-1-end-to-end-diagram]

Dans cette leçon, vous configurez votre carte Arduino avec un système d’exploitation, vous installez votre environnement de développement et vous déployez une application sur votre carte Arduino.

### <a name="configure-your-device"></a>Configurer votre appareil
Configurez votre carte Arduino pour une première utilisation en assemblant la carte pour la mettre sous tension.

*Durée estimée : 5 minutes*

Accédez à [Configurer votre appareil][configure-your-device].

### <a name="get-the-tools"></a>Obtenir les outils
Téléchargez les outils et les logiciels nécessaires pour générer et déployer votre première application pour votre carte Arduino.

*Durée estimée : 20 minutes*

Accédez à [Obtenir les outils][get-the-tools].

### <a name="create-and-deploy-the-blink-application"></a>Créer et déployer l’application blink
Clonez l’exemple d’application de clignotement (blink) Arduino à partir de GitHub et utilisez gulp pour déployer cette application sur votre carte Arduino. Avec cet exemple d’application, la LED embarquée GPIO #13 clignote toutes les deux secondes.

*Durée estimée : 5 minutes*

Accédez à [Créer et déployer l’application Blink][create-and-deploy-the-blink-application].

## <a name="lesson-2-create-your-iot-hub"></a>Leçon 2 : Création de votre IoT Hub
![Diagramme de bout en bout pour la leçon 2][lesson-2-end-to-end-diagram]

Dans cette leçon, vous créez votre compte Azure gratuit, vous approvisionnez votre Azure IoT Hub et vous créez votre premier appareil dans IoT Hub.

Avant de commencer cette leçon, terminez la Leçon 1.

### <a name="get-the-azure-tools"></a>Obtenir les outils Azure
Installez l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Obtenir les outils Azure][get-azure-tools].

### <a name="create-your-iot-hub-and-register-your-arduino-board"></a>Créer votre IoT Hub et inscrire votre carte Arduino
Créez votre groupe de ressources, approvisionnez votre premier Azure IoT Hub et ajoutez votre premier appareil à IoT Hub à l’aide de l’interface de ligne de commande Azure.

*Durée estimée : 10 minutes*

Accédez à [Créer votre IoT Hub et inscrire votre carte Arduino][create-your-iot-hub-and-register-your-arduino-board].

## <a name="lesson-3-send-device-to-cloud-messages"></a>Leçon 3 : Envoyer des messages appareil-à-cloud
![Diagramme de bout en bout pour la leçon 3][lesson-3-end-to-end-diagram]

Dans cette leçon, vous envoyez des messages de votre carte Arduino à votre IoT Hub. Vous créez également une application de fonction Azure qui récupère les messages entrants à partir de votre IoT Hub et les écrit dans le Stockage Table Azure.

Avant de commencer cette leçon, terminez les leçons 1 et 2.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Création d’une application de fonction Azure et d’un compte de stockage Azure
Utilisez un modèle Azure Resource Manager pour créer une application de fonction Azure et un compte de stockage Azure.

*Durée estimée : 10 minutes*

Accédez à [Création d’une application Azure Function App et d’un compte de stockage Azure][create-an-azure-function-app-and-azure-storage-account].

### <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Exécution d’un exemple d’application pour envoyer des messages appareil-à-cloud
Déployez et exécutez un exemple d’application sur votre carte Arduino qui envoie des messages à IoT Hub.

*Durée estimée : 10 minutes*

Accédez à [Exécution d’un exemple d’application pour envoyer des messages appareil vers cloud][send-device-to-cloud-messages].

### <a name="read-messages-persisted-in-azure-storage"></a>Lecture des messages conservés dans le stockage Azure
Surveillez les messages appareil-à-cloud alors qu’ils sont écrits dans le stockage Azure.

*Durée estimée : 5 minutes*

Accédez à [Lecture des messages conservés dans le stockage Azure][read-messages-persisted-in-azure-storage].

## <a name="lesson-4-send-cloud-to-device-messages"></a>Leçon 4 : Envoi de messages cloud-à-appareil
![Diagramme de bout en bout pour la leçon 4][lesson-4-end-to-end-diagram]

Cette leçon vous montre comment envoyer des messages de votre Azure IoT Hub à votre carte Arduino. Les messages contrôlent le comportement activé/désactivé de la LED embarquée GPIO #13. Un exemple d’application est préparé pour vous aider à exécuter cette tâche.

Avant de commencer cette leçon, terminez les leçons 1, 2 et 3.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Exécution de l’exemple d’application pour recevoir des messages cloud-à-appareil
L’exemple d’application de la leçon 4 s’exécute sur votre carte Arduino et surveille les messages entrants à partir de votre IoT Hub. Une nouvelle tâche gulp envoie des messages à votre carte Arduino à partir de votre IoT Hub pour faire clignoter la LED.

*Durée estimée : 10 minutes*

Accédez à [Exécution de l’exemple d’application pour recevoir des messages cloud vers appareil][receive-cloud-to-device-messages].

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Section facultative : Modification du comportement activé/désactivé de la LED
Personnalisez les messages pour modifier le comportement activé/désactivé de la LED.

*Durée estimée : 10 minutes*

Accédez à [Section facultative : Modification du comportement activé/désactivé de la LED][change-the-on-and-off-behavior-of-the-led].

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous rencontrez des problèmes au cours des leçons, recherchez des solutions dans l’article [Résolution des problèmes][troubleshooting].

<!-- Images and links -->

[Lesson-1-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson1.png
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
[lesson-2-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson2.png
[get-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[lesson-3-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson3.png
[create-an-azure-function-app-and-azure-storage-account]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md
[read-messages-persisted-in-azure-storage]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-read-table-storage.md
[lesson-4-end-to-end-diagram]: media/iot-hub-adafruit-feather-m0-wifi-lessons/e2e-lesson4.png
[receive-cloud-to-device-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-send-cloud-to-device-messages.md
[change-the-on-and-off-behavior-of-the-led]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson4-change-led-behavior.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md


<!--HONumber=Dec16_HO2-->


