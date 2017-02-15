---
title: Configurer votre starter kit IoT Azure | Microsoft Docs
description: "Configurez Adafruit Feather M0 WiFi pour une première utilisation."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "configuration arduino, connecter arduino à un pc, configurer arduino, carte arduino"
ms.assetid: f5b334f0-a148-41aa-b374-ce7b9f5b305a
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: fd3f55ee79bdd4d485ae3a479a5a072905956bc9
ms.openlocfilehash: 47d7a4e68574c5f88b336e9ca7b21dbba24a94cf


---
# <a name="configure-your-device"></a>Configurer votre appareil
## <a name="what-you-will-do"></a>Procédure à suivre
Configurez votre carte Arduino Adafruit Feather M0 WiFi pour une première utilisation en assemblant la carte pour la mettre sous tension. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-you-need"></a>Ce dont vous avez besoin
Pour cette opération, vous aurez besoin des composants suivants pour votre starter kit Adafruit Feather M0 WiFi :

* La carte Adafruit Feather M0 WiFi
* Un câble Micro USB B vers Type A

![kit][kit]

Vous aurez également besoin des éléments suivants :

* Un ordinateur exécutant Windows, Mac ou Linux.
* Une connexion sans fil pour la connexion de votre carte Arduino.
* Une connexion Internet pour télécharger l’outil de configuration.

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :

* Comment assembler votre carte Arduino et la mettre sous tension pour les leçons suivantes.
* Comment ajouter des autorisations de port série sous Ubuntu.

## <a name="connect-your-arduino-board-to-your-computer"></a>Connecter la carte Arduino à votre ordinateur

1. Connectez le câble micro USB au port micro USB du haut.

   ![Port micro USB du haut][top-micro-usb-port]

2. Branchez l’autre extrémité du câble USB à votre ordinateur.

   ![USB de l’ordinateur][computer-usb]

## <a name="add-serial-port-permissions-on-ubuntu"></a>Ajouter des autorisations de port série sous Ubuntu

Vous pouvez ignorer cette section si vous utilisez Windows ou macOS. Pour Ubuntu, vous devez suivre les étapes suivantes afin que l’utilisateur Linux normal dispose bien des autorisations nécessaires pour utiliser le port USB de votre carte Arduino.

1. À présent, en tant qu’utilisateur normal à partir du terminal :

   ```bash
   ls -l /dev/ttyUSB*
   # Or
   ls -l /dev/ttyACM*
   ```

   Vous obtenez le résultat suivant :

   ```bash
   crw-rw---- 1 root uucp 188, 0 5 apr 23.01 ttyUSB0
   # Or
   crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyACM0
   ```

   Le « 0 » peut être un autre nombre, et plusieurs entrées peuvent être retournées. Dans le premier cas, nous avons besoin de la donnée `uucp`, dans le second de la donnée `dialout`, qui est le propriétaire du groupe du fichier.

2. Ajouter un utilisateur au groupe :

   ```bash
   sudo usermod -a -G group-name username
   ```

   Où `group-name` correspond à la donnée trouvée lors de la première étape et `username` à votre nom d’utilisateur Linux.

3. Vous devrez vous déconnecter et vous reconnecter pour que cette modification prenne effet et pour terminer l’installation.

## <a name="summary"></a>Résumé
Dans cet article, vous avez appris à configurer votre carte Arduino. La tâche suivante consiste à installer les outils et logiciels nécessaires en vue d’exécuter un exemple d’application sur votre carte Arduino.

![Le matériel est prêt.][hardware-is-ready]

## <a name="next-steps"></a>Étapes suivantes
[Obtenez les outils][get-the-tools]
<!-- Images and links -->

[kit]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/kit.png
[top-micro-usb-port]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/top_usbport.jpg
[computer-usb]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/computer_usb.jpg
[hardware-is-ready]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/hardware_ready.jpg
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md


<!--HONumber=Dec16_HO2-->


