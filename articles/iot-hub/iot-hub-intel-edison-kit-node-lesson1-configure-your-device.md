---
title: "Création et déploiement de l’application de clignotement (blink) dans le Kit de démarrage Azure IoT | Microsoft Docs"
description: "Configurez Intel Edison pour une première utilisation."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "configuration arduino, connecter arduino à un pc, configurer arduino, carte arduino"
ms.assetid: 372c9b6d-e701-4ff6-8151-d262aa76aa55
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/7/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: f45b3bf00d619376ac07418f0c02eca5f3241939
ms.openlocfilehash: 4511f88bc6a3e71fe3ea9375711dade15fd4ef70


---
# <a name="configure-your-intel-edison"></a>Configurer votre Intel Edison
## <a name="what-you-will-do"></a>Procédure à suivre
Configurez Intel Edison pour une première utilisation en assemblant la carte, en la mettant sous tension et en installant l’outil de configuration sur le système d’exploitation de votre ordinateur afin de flasher le microprogramme d’Edison, de définir son mot de passe et de le connecter au réseau Wi-Fi. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :

* Comment assembler la carte Edison et la mettre sous tension.
* Comment flasher le microprogramme d’Edison, définir un mot de passe et connecter le Wi-Fi.

## <a name="what-you-need"></a>Ce dont vous avez besoin
Pour cette opération, vous aurez besoin des composants suivants de votre Kit de démarrage Intel Edison :

* Module Intel® Edison
* Carte d’extension Arduino
* Les barres de séparation ou vis incluses dans l’emballage, y compris deux vis permettant de fixer le module sur la carte d’extension et quatre jeux de vis et séparateurs en plastique.
* Un câble USB Micro B vers Type A
* Une alimentation en courant continu (CC). La puissance nominale de l’alimentation doit être :
  - 7-15 V CC
  - Au moins 1 500 mA
  - La broche centrale/intérieure doit être le pôle positif de l’alimentation

  ![Éléments de votre Starter Kit](media/iot-hub-intel-edison-lessons/lesson1/kit.png)

Vous aurez également besoin des éléments suivants :

* Un ordinateur exécutant Windows, Mac ou Linux.
* Une connexion sans fil pour la connexion d’Edison.
* Une connexion Internet pour télécharger l’outil de configuration.

## <a name="assemble-your-board"></a>Assembler votre carte

Cette section présente les étapes permettant de connecter votre module Intel® Edison à votre carte d’extension.

1. Placez le module Intel® Edison à l’intérieur du cadre blanc sur votre carte d’extension, en alignant les trous du module avec les vis de la carte d’extension.

2. Appuyez sur le module juste en dessous de l’indication `What will you make?` jusqu'à entendre un clic.

   ![assemblage de carte 2](media/iot-hub-intel-edison-lessons/lesson1/assemble_board2.jpg)

3. Utilisez les deux écrous hexagonaux (inclus dans le package) pour fixer le module sur la carte d’extension.

   ![assemblage de carte 3](media/iot-hub-intel-edison-lessons/lesson1/assemble_board3.jpg)

4. Insérez une vis dans un des quatre trous situés dans les coins de la carte d’extension. Faites tourner et fixez un des séparateurs en plastique blancs sur la vis.

   ![assemblage de carte 4](media/iot-hub-intel-edison-lessons/lesson1/assemble_board4.jpg)

5. Répétez l'opération pour les trois autres séparateurs situés dans les coins.

   ![assemblage de carte 5](media/iot-hub-intel-edison-lessons/lesson1/assemble_board5.jpg)

Votre carte est maintenant assemblée.

   ![carte assemblée](media/iot-hub-intel-edison-lessons/lesson1/assembled_board.jpg)

## <a name="power-up-edison"></a>Mise sous tension d’Edison

1. Branchez le bloc d’alimentation.

   ![Branchement du bloc d'alimentation](media/iot-hub-intel-edison-lessons/lesson1/plug_power.jpg)

2. Une LED verte (marquée DS1 sur la carte d’extension Arduino*) doit s’allumer et rester allumée.

3. Attendez une minute que la carte finisse de démarrer.

   > [!NOTE]
   > Si vous ne disposez pas d’un bloc d’alimentation CC, vous pouvez toujours alimenter la carte via un port USB. Consultez la section `Connect Edison to your computer` pour plus d’informations. Cette procédure de mise sous tension de votre carte peut entraîner un comportement imprévisible de la carte, en particulier si vous utilisez un réseau Wi-Fi ou des moteurs d’entraînement.

## <a name="connect-edison-to-your-computer"></a>Connexion d’Edison à votre ordinateur

1. Basculez le microcommutateur vers les deux ports micro-USB afin de placer Edison est en mode appareil. Veuillez vous reporter [ici](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode) pour connaître les différences entre le mode appareil et le mode hôte.

   ![Basculement du microcommutateur vers le bas](media/iot-hub-intel-edison-lessons/lesson1/toggle_down_microswitch.jpg)

2. Connectez le câble micro USB au port micro USB du haut.

   ![Port micro USB du haut](media/iot-hub-intel-edison-lessons/lesson1/top_usbport.jpg)

3. Branchez l’autre extrémité du câble USB à votre ordinateur.

   ![USB de l’ordinateur](media/iot-hub-intel-edison-lessons/lesson1/computer_usb.jpg)

4. Vous saurez que votre carte est entièrement initialisée lorsque l’ordinateur monte un nouveau lecteur (comme lorsque vous insérez une carte SD dans votre ordinateur).

## <a name="download-and-run-the-configuration-tool"></a>Téléchargement et exécution de l’outil de configuration
Consultez [ce lien](https://software.intel.com/en-us/iot/hardware/edison/downloads) répertorié sous le titre `Installers` pour obtenir le dernier outil de configuration. Exécutez l’outil et suivez ses instructions à l'écran en cliquant sur Suivant si nécessaire.

### <a name="flash-firmware"></a>Flashage du microprogramme
1. Sur la page `Set up options`, cliquez sur `Flash Firmware`.
2. Sélectionnez l’image à flasher sur votre carte en effectuant l’une des opérations suivantes :
   - Pour télécharger et flasher votre carte avec la dernière image de microprogramme disponible d’Intel, sélectionnez `Download the latest image version xxxx`.
   - Pour flasher votre carte avec une image que vous avez déjà enregistrée sur votre ordinateur, sélectionnez `Select the local image`. Recherchez et sélectionnez l’image à flasher sur votre carte.
3. L’outil d’installation tente de flasher votre carte. L’ensemble du processus de flashage peut prendre jusqu’à 10 minutes.

### <a name="set-password"></a>Définition du mot de passe
1. Sur la page `Set up options`, cliquez sur `Enable Security`.
2. Vous pouvez attribuer un nom personnalisé à votre carte Intel® Edison. Cette étape est facultative.
3. Tapez un mot de passe pour votre carte, puis cliquez sur `Set password`.
4. Notez le mot de passe, qui sera utilisé ultérieurement.

### <a name="connect-wi-fi"></a>Connexion Wi-Fi
1. Sur la page `Set up options`, cliquez sur `Connect Wi-Fi`. Attendez environ une minute que votre ordinateur recherche les réseaux Wi-Fi disponibles.
2. Dans la liste déroulante `Detected Networks`, sélectionnez votre réseau.
3. Dans la liste déroulante `Security`, sélectionnez le type de sécurité du réseau.
4. Indiquez vos identifiant et mot de passe, puis cliquez sur `Configure Wi-Fi`.
5. Notez l’adresse IP, qui sera utilisée ultérieurement.

> [!NOTE]
> Assurez-vous qu’Edison est connecté au même réseau que votre ordinateur. Votre ordinateur se connecte à votre Edison à l’aide de l’adresse IP.

Félicitations ! Vous avez réussi à configurer Edison.

## <a name="summary"></a>Résumé
Dans cet article, vous avez appris à assembler la carte Edison, flasher son microprogramme, définir un mot de passe et connecter la carte au réseau Wi-Fi à l’aide de l’outil de configuration. Notez que la LED n’est pas encore allumée. La tâche suivante consiste à installer les outils et le logiciel nécessaires en vue d’exécuter un exemple d’application sur Edison.

## <a name="next-steps"></a>Étapes suivantes
[Obtenez les outils][get-the-tools]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md


<!--HONumber=Dec16_HO2-->


