---
title: "Raspberry Pi vers cloud (C) - Connecter Raspberry Pi à Azure IoT Hub | Microsoft Docs"
description: "Dans ce didacticiel, découvrez comment configurer et connecter Raspberry Pi à Azure IoT Hub pour lui permettre d’envoyer des données à la plateforme cloud Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure iot raspberry pi, raspberry pi iot hub, raspberry pi envoie des données vers le cloud, raspberry pi vers cloud"
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/12/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b8fda17a8d1d1796d5299e3aba4b0fd5e719a4c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Connecter Raspberry Pi à Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux de l’utilisation de Raspberry Pi exécutant Raspbian. Vous apprenez ensuite à connecter en toute transparence vos appareils au cloud avec [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Pour obtenir des exemples Windows 10 IoT Standard, accédez au [centre de développement Windows](http://www.windowsondevices.com/).

Vous n’avez pas encore de kit ? Essayez [le simulateur en ligne Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Ou achetez un nouveau kit [ici](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Procédure

* Créez un hub IoT.
* Inscrivez un appareil pour Pi dans votre IoT Hub.
* Configurez Raspberry Pi.
* Exécutez un exemple d’application sur Pi pour envoyer des données de capteur à votre IoT Hub.

Connectez Raspberry Pi à un IoT Hub créé à cette fin. Exécutez ensuite un exemple d’application sur Pi pour collecter des données de température et d’humidité provenant d’un capteur BME280. Enfin, envoyez les données du capteur à votre IoT Hub.

## <a name="what-you-learn"></a>Contenu

* Création d’un Azure IoT Hub et obtention de la chaîne de connexion de votre nouvel appareil.
* Connexion de Pi à un capteur BME280.
* Collecte des données du capteur en exécutant un exemple d’application sur Pi.
* Envoi des données du capteur à votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

![Ce dont vous avez besoin](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Une carte Raspberry Pi 2 ou Raspberry Pi 3.
* Un abonnement Azure actif. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation Azure gratuit](https://azure.microsoft.com/free/) en quelques minutes.
* Un moniteur, un clavier USB et une souris qui se connectent à Pi.
* Un Mac ou un PC exécutant Windows ou Linux.
* Une connexion Internet.
* Une carte microSD d’au moins 16 Go.
* Un adaptateur USB-SD ou une carte microSD pour graver l’image du système d’exploitation sur la carte microSD.
* Une alimentation 5 V 2 A avec le câble micro USB de 6 pieds (env. 183 cm).

Les éléments suivants sont facultatifs :

* Un capteur de température, de pression et d’humidité Adafruit BME280 assemblé.
* Une platine d’expérimentation.
* 6 câbles de liaison F/M.
* Une LED de 10 mm diffuse.


> [!NOTE] 
Ces éléments sont facultatifs, car l’exemple de code prend en charge les données de capteur simulées.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Configuration de Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installation du système d’exploitation Raspbian pour Pi

Préparez la carte microSD pour l’installation de l’image Raspbian.

1. Téléchargez Raspbian.
   1. [Téléchargez Raspbian Jessie avec Desktop](https://www.raspberrypi.org/downloads/raspbian/) (fichier .zip).
   1. Extrayez l’image Raspbian dans un dossier sur votre ordinateur.
1. Installez Raspbian sur la carte microSD.
   1. [Téléchargez et installez l’utilitaire de graveur de carte SD Etcher](https://etcher.io/).
   1. Exécutez Etcher et sélectionnez l’image Raspbian extraite à l’étape 1.
   1. Sélectionnez le lecteur de carte microSD. Remarque : Etcher a peut-être déjà sélectionné le lecteur approprié.
   1. Cliquez sur Flash pour installer Raspbian sur la carte microSD.
   1. Retirez la carte microSD de votre ordinateur une fois l’installation terminée. Vous pouvez retirer directement la carte microSD en toute sécurité, car Etcher éjecte ou démonte automatiquement la carte microSD une fois le processus terminé.
   1. Insérez la carte microSD dans Pi.

### <a name="enable-ssh-and-spi"></a>Activer SSH et SPI

1. Connectez Pi au moniteur, au clavier et à la souris, démarrez Pi puis connectez-vous à Raspbian en utilisant `pi` comme nom d’utilisateur et `raspberry` comme mot de passe.
1. Cliquez sur l’icône Raspberry > **Préférences** > **Configuration de Raspberry Pi**.

   ![Le menu Préférences de Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Sur l’onglet **Interfaces**, définissez **SPI** et **SSH** sur **Activer**, puis cliquez sur **OK**. Si vous n’avez pas de capteurs physiques et que vous souhaitez utiliser des données de capteur simulé, cette étape est facultative.

   ![Activer SPI et SSH sur Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Pour activer SSH et SPI, vous trouverez d’autres documents de référence sur [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) et [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Connecter le capteur à Pi

Utilisez la platine d’expérimentation et les câbles de liaison pour connecter une LED et un BME280 à Pi comme suit. Si vous ne disposez pas de ce capteur, [ignorez cette section](#connect-pi-to-the-network).

![La connexion entre Raspberry Pi et le capteur](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

Le capteur BME280 peut collecter des données sur la température et l’humidité. Et la DEL clignote s’il existe une communication entre l’appareil et le cloud. 

Pour les broches du capteur, utilisez le câblage suivant :

| Début (capteur et LED)     | Fin (carte)            | Couleur du câble   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (broche 5G)         | GPIO 4 (broche 7)         | Câble blanc   |
| LED GND (broche 6G)         | GND (broche 6)            | Câble noir   |
| VDD (broche 18F)            | ALIM 3,3V (broche 17)      | Câble blanc   |
| GND (broche 20F)            | GND (broche 20)           | Câble noir   |
| SCK (broche 21F)            | SPI0 SCLK (broche 23)     | Câble orange  |
| SDO (broche 22F)            | SPI0 MISO (broche 21)     | Câble jaune  |
| SDI (broche 23F)            | SPI0 MOSI (broche 19)     | Câble vert   |
| CS (broche 24F)             | SPI0 CS (broche 24)       | Câble bleu    |

Cliquez pour afficher les [mappages de broches de Raspberry Pi 2 et 3](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) à titre de référence.

Une fois le BME280 connecté à votre Raspberry Pi, il doit se présenter comme sur l’image ci-dessous.

![Pi et BME280 connectés](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Connexion de Pi au réseau

Mettez Pi sous tension à l’aide du câble micro USB et de l’alimentation. Utilisez le câble Ethernet pour connecter Pi à votre réseau câblé ou suivez les [instructions fournies par la Fondation Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) pour connecter Pi à votre réseau sans fil. Une fois que votre Pi est correctement connecté au réseau, vous devez relever son [adresse IP](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Connecté au réseau câblé](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Exécuter un exemple d’application sur Pi

### <a name="install-the-prerequisite-packages"></a>Installer les packages requis

1. Utilisez l’un des clients SSH suivants à partir de votre ordinateur hôte pour vous connecter à votre Raspberry Pi.
   
   **Utilisateurs Windows**
   1. Téléchargez et installez [PuTTY](http://www.putty.org/) pour Windows. 
   1. Copiez l’adresse IP de votre Pi dans la section du nom d’hôte (ou adresse IP) et sélectionnez le type de connexion SSH.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Utilisateurs Mac et Ubuntu**
   
   Utilisez le client SSH intégré sur Ubuntu ou macOS. Vous devrez peut-être exécuter `ssh pi@<ip address of pi>` pour connecter le Pi via le protocole SSH.
   > [!NOTE] 
   Le nom d’utilisateur par défaut est `pi` et le mot de passe est `raspberry`.

1. Installez les packages requis pour le Microsoft Azure IoT Device SDK pour C et Cmake en exécutant les commandes suivantes :

   ```bash
   grep -q -F 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   grep -q -F 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' /etc/apt/sources.list || sudo sh -c "echo 'deb-src http://ppa.launchpad.net/aziotsdklinux/ppa-azureiot/ubuntu vivid main' >> /etc/apt/sources.list"
   sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FDA6A393E4C2257F
   sudo apt-get update
   sudo apt-get install -y azure-iot-sdk-c-dev cmake libcurl4-openssl-dev git-core
   git clone git://git.drogon.net/wiringPi
   cd ./wiringPi
   ./build
   ```


### <a name="configure-the-sample-application"></a>Configurer l’exemple d’application

1. Clonez l’exemple d’application en exécutant la commande suivante :

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app
   ```
1. Ouvrez le fichier de configuration en exécutant les commandes suivantes :

   ```bash
   cd iot-hub-c-raspberrypi-client-app
   nano config.h
   ```

   ![Fichier de configuration](media/iot-hub-raspberry-pi-kit-c-get-started/6_config-file.png)

   Deux éléments de type macros peuvent être configurés dans ce fichier. Le premier est `INTERVAL`, qui définit le délai (en millisecondes) entre deux messages envoyés au cloud. Le deuxième est `SIMULATED_DATA`, qui est une valeur booléenne pour l’utilisation ou non des données de capteur simulées.

   Si vous **ne disposez pas du capteur**, définissez la valeur `SIMULATED_DATA` sur `1` pour que l’exemple d’application crée et utilise des données de capteur simulées.

1. Enregistrez et quittez en appuyant sur Ctrl-O > Entrée > Ctrl-X.

### <a name="build-and-run-the-sample-application"></a>Générer et exécuter l'exemple d’application

1. Générez l’exemple d’application en exécutant la commande suivante :

   ```bash
   cmake . && make
   ```
   ![Sortie de la génération](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Exécutez l’exemple d’application en exécutant la commande suivante :

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Assurez-vous de copier-coller la chaîne de connexion de l’appareil entre les guillemets simples.


Vous devriez voir le résultat suivant, qui affiche les données de capteur et les messages envoyés à votre IoT Hub.

![Sortie - données de capteur envoyées depuis Raspberry Pi vers votre IoT Hub](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Étapes suivantes

Vous avez exécuté un exemple d’application pour collecter des données de capteur et les envoyer à votre IoT Hub. Pour voir les messages envoyés par Raspberry Pi à votre IoT Hub ou pour envoyer des messages à votre Raspberry Pi depuis une interface de ligne de commande, consultez le [tutoriel Gérer une messagerie cloud vers appareil avec l’explorateur IoT hub](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
