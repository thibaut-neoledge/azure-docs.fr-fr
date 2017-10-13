---
title: "Intel Edison vers le cloud (Node.js) - Connecter Intel Edison à Azure IoT Hub | Microsoft Docs"
description: "Dans ce didacticiel, découvrez comment configurer et connecter Intel Edison à Azure IoT Hub pour lui permettre d’envoyer des données à la plateforme cloud Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot intel edison, intel edison iot hub, intel edison envoyer des données vers le cloud, intel edison vers le cloud"
ms.assetid: a7c9cf2d-c102-41b0-aa45-41285c6877eb
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/15/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a31efba704045196b5563f7bc467c773bea7805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-intel-edison-to-azure-iot-hub-nodejs"></a>Connecter Intel Edison à Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux de l’utilisation d’Intel Edison. Vous apprenez ensuite à connecter en toute transparence vos appareils au cloud avec [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

Vous n’avez pas encore de kit ? Démarrer [ici](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Procédure

* Configurez Intel Edison et les modules Grove.
* Créez un hub IoT.
* Inscrivez un appareil pour Edison dans votre IoT Hub.
* Exécutez un exemple d’application sur Edison pour envoyer des données de capteur à votre IoT Hub.

Connectez Intel Edison à un IoT Hub créé à cette fin. Exécutez ensuite un exemple d’application sur Edison pour collecter des données de température et d’humidité provenant d’un capteur de température Grove. Enfin, envoyez les données du capteur à votre IoT Hub.

## <a name="what-you-learn"></a>Contenu

* Création d’un Azure IoT Hub et obtention de la chaîne de connexion de votre nouvel appareil.
* Connexion d’Edison à un capteur de température Grove.
* Collecte des données du capteur en exécutant un exemple d’application sur Edison.
* Envoi des données du capteur à votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

![Ce dont vous avez besoin](media/iot-hub-intel-edison-kit-node-get-started/0_kit.png)

* La carte Intel Edison
* Carte d’extension Arduino
* Un abonnement Azure actif. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation Azure gratuit](https://azure.microsoft.com/free/) en quelques minutes.
* Un Mac ou un PC exécutant Windows ou Linux.
* Une connexion Internet.
* Un câble USB Micro B vers Type A
* Une alimentation en courant continu (CC). La puissance nominale de l’alimentation doit être :
  - 7-15 V CC
  - Au moins 1 500 mA
  - La broche centrale/intérieure doit être le pôle positif de l’alimentation

Les éléments suivants sont facultatifs :

* Grove Base Shield V2
* Capteur de température Grove
* Câble Grove
* Les barres de séparation ou vis incluses dans l’emballage, y compris deux vis permettant de fixer le module sur la carte d’extension et quatre jeux de vis et séparateurs en plastique.

> [!NOTE] 
Ces éléments sont facultatifs, car l’exemple de code prend en charge les données de capteur simulées.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Configuration d’Intel Edison

### <a name="assemble-your-board"></a>Assembler votre carte

Cette section présente les étapes permettant de connecter votre module Intel® Edison à votre carte d’extension.

1. Placez le module Intel® Edison à l’intérieur du cadre blanc sur votre carte d’extension, en alignant les trous du module avec les vis de la carte d’extension.

2. Appuyez sur le module juste en dessous de l’indication `What will you make?` jusqu'à entendre un clic.

   ![assemblage de carte 2](media/iot-hub-intel-edison-kit-node-get-started/1_assemble_board2.jpg)

3. Utilisez les deux écrous hexagonaux (inclus dans le package) pour fixer le module sur la carte d’extension.

   ![assemblage de carte 3](media/iot-hub-intel-edison-kit-node-get-started/2_assemble_board3.jpg)

4. Insérez une vis dans un des quatre trous situés dans les coins de la carte d’extension. Faites tourner et fixez un des séparateurs en plastique blancs sur la vis.

   ![assemblage de carte 4](media/iot-hub-intel-edison-kit-node-get-started/3_assemble_board4.jpg)

5. Répétez l'opération pour les trois autres séparateurs situés dans les coins.

   ![assemblage de carte 5](media/iot-hub-intel-edison-kit-node-get-started/4_assemble_board5.jpg)

Votre carte est maintenant assemblée.

   ![carte assemblée](media/iot-hub-intel-edison-kit-node-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Connecter le module Grove Base Shield et le capteur de température

1. Placez le module Grove Base Shield sur votre carte. Assurez-vous que toutes les broches sont bien insérées dans votre carte.
   
   ![Grove Base Shield](media/iot-hub-intel-edison-kit-node-get-started/6_grove_base_sheild.jpg)

2. Utilisez un câble Grove pour connecter le capteur de température Grove sur le port **A0** du module Grove Base Shield.

   ![Connexion au capteur de température](media/iot-hub-intel-edison-kit-node-get-started/7_temperature_sensor.jpg)

   ![Connexion d’Edison et du capteur](media/iot-hub-intel-edison-kit-node-get-started/16_edion_sensor.png)

Votre capteur est maintenant prêt.

### <a name="power-up-edison"></a>Mise sous tension d’Edison

1. Branchez le bloc d’alimentation.

   ![Branchement du bloc d'alimentation](media/iot-hub-intel-edison-kit-node-get-started/8_plug_power.jpg)

2. Une LED verte (marquée DS1 sur la carte d’extension Arduino*) doit s’allumer et rester allumée.

3. Attendez une minute que la carte finisse de démarrer.

   > [!NOTE]
   > Si vous ne disposez pas d’un bloc d’alimentation CC, vous pouvez toujours alimenter la carte via un port USB. Consultez la section `Connect Edison to your computer` pour plus d’informations. Cette procédure de mise sous tension de votre carte peut entraîner un comportement imprévisible de la carte, en particulier si vous utilisez un réseau Wi-Fi ou des moteurs d’entraînement.

### <a name="connect-edison-to-your-computer"></a>Connexion d’Edison à votre ordinateur

1. Basculez le microcommutateur vers les deux ports micro-USB afin de placer Edison est en mode appareil. Veuillez vous reporter [ici](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode) pour connaître les différences entre le mode appareil et le mode hôte.

   ![Basculement du microcommutateur vers le bas](media/iot-hub-intel-edison-kit-node-get-started/9_toggle_down_microswitch.jpg)

2. Connectez le câble micro USB au port micro USB du haut.

   ![Port micro USB du haut](media/iot-hub-intel-edison-kit-node-get-started/10_top_usbport.jpg)

3. Branchez l’autre extrémité du câble USB à votre ordinateur.

   ![USB de l’ordinateur](media/iot-hub-intel-edison-kit-node-get-started/11_computer_usb.jpg)

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

   ![Connexion au capteur de température](media/iot-hub-intel-edison-kit-node-get-started/12_configuration_tool.png)

Félicitations ! Vous avez réussi à configurer Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Exécution d’un exemple d’application sur Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Préparer le SDK Azure IoT Device

1. Utilisez l’un des clients SSH suivants à partir de votre ordinateur hôte pour vous connecter à votre Intel Edison. L’adresse IP est obtenue à partir de l’outil de configuration et le mot de passe est celui que vous avez défini dans cet outil.
    - [PuTTY](http://www.putty.org/) pour Windows.
    - Le client SSH intégré sur Ubuntu ou macOS.

2. Clonez l’exemple d’application cliente sur votre appareil. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-client-app
   ```

3. Accédez ensuite au dossier de référentiel pour exécuter la commande suivante afin d’installer tous les packages (la procédure peut prendre quelques minutes).
   
   ```bash
   cd iot-hub-node-intel-edison-client-app
   npm install
   ```


### <a name="configure-and-run-the-sample-application"></a>Configurer et exécuter l’exemple d’application

1. Ouvrez le fichier de configuration en exécutant les commandes suivantes :

   ```bash
   nano config.json
   ```

   ![Fichier de configuration](media/iot-hub-intel-edison-kit-node-get-started/13_configure_file.png)

   Deux éléments de type macros peuvent être configurés dans ce fichier. Le premier est `INTERVAL`, qui définit le délai entre deux messages envoyés au cloud. Le deuxième est `simulatedData`, qui est une valeur booléenne pour l’utilisation ou non des données de capteur simulées.

   Si vous **ne disposez pas du capteur**, définissez la valeur `simulatedData` sur `true` pour que l’exemple d’application crée et utilise des données de capteur simulées.

1. Enregistrez et quittez en appuyant sur Ctrl-O > Entrée > Ctrl-X.


1. Exécutez l’exemple d’application en exécutant la commande suivante :

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Assurez-vous de copier-coller la chaîne de connexion de l’appareil entre les guillemets simples.

Vous devriez voir le résultat suivant, qui affiche les données de capteur et les messages envoyés à votre IoT Hub.

![Sortie : données de capteur envoyées depuis Intel Edison vers votre IoT Hub](media/iot-hub-intel-edison-kit-node-get-started/15_message_sent.png)

## <a name="next-steps"></a>Étapes suivantes

Vous avez exécuté un exemple d’application pour collecter des données de capteur et les envoyer à votre IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
