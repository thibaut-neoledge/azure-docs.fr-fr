---
title: "M0 vers cloud : Connecter la carte Feather M0 WiFi à Azure IoT Hub | Microsoft Docs"
description: "Ce didacticiel explique comment configurer la carte Adafruit Feather M0 WiFi et la connecter à Azure IoT Hub pour envoyer des données à la plateforme cloud Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/12/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: 8eee4b2eea165176bddf0896685636bbac4db321
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---

# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Connecter Adafruit Feather M0 WiFi à Azure IoT Hub dans le cloud
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Connexion entre un capteur BME280, une carte Feather M0 WiFi et IoT Hub](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

Dans ce didacticiel, vous commencez par découvrir les principes fondamentaux de l’utilisation de votre carte Arduino. Vous apprenez ensuite à connecter en toute transparence vos appareils au cloud avec [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>Procédure

Connectez Adafruit Feather M0 WiFi à un IoT Hub créé à cette fin. Exécutez ensuite un exemple d’application sur M0 WiFi pour collecter les données de température et d’humidité provenant d’un capteur BME280. Enfin, envoyez les données du capteur à votre IoT Hub.


## <a name="what-you-learn"></a>Contenu

* Création d’un IoT Hub et inscription d’un appareil pour la carte Feather M0 WiFi
* Connexion de la carte Feather M0 WiFi au capteur et à votre ordinateur
* Collecte des données du capteur en exécutant un exemple d’application sur la carte Feather M0 WiFi
* Envoi des données du capteur à votre IoT Hub

## <a name="what-you-need"></a>Ce dont vous avez besoin

![Composants requis pour le didacticiel](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Pour cette opération, vous aurez besoin des composants suivants de votre Kit de démarrage Feather M0 WiFi :

* La carte Feather M0 WiFi
* Un câble Micro USB vers USB Type A

Vous aurez également besoin des éléments suivants pour votre environnement de développement :

* Un abonnement Azure actif. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation Azure gratuit](https://azure.microsoft.com/free/) en quelques minutes.
* Un Mac ou un PC exécutant Windows ou Ubuntu.
* Un réseau sans fil auquel connecter la carte Feather M0 WiFi.
* Une connexion Internet pour télécharger l’outil de configuration.
* [Arduino IDE](https://www.arduino.cc/en/main/software) version 1.6.8 ou ultérieure. Les versions antérieures ne fonctionneront pas avec la bibliothèque Azure IoT Hub.

Si vous n’avez pas de capteur, les éléments suivants sont facultatifs. Vous avez également la possibilité d’utiliser des données de capteur simulées :

* Un capteur de température et d’humidité BME280
* Une platine d’expérimentation
* Des câbles de liaison M/M

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Connecter la carte Feather M0 WiFi au capteur et à votre ordinateur
Dans cette section, vous connectez les capteurs à votre carte. Puis vous connectez votre appareil à votre ordinateur pour une utilisation ultérieure.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Connecter un capteur de température et d’humidité DHT22 à la carte Feather M0 WiFi

Utilisez la platine d’expérimentation et les câbles de liaison pour effectuer la connexion. Si vous n’avez pas de capteur, ignorez cette section. Vous pourrez utiliser des données de capteur simulées à la place.

![Schéma des connexions](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Pour les broches du capteur, utilisez le câblage suivant :


| Début (capteur)           | Fin (carte)            | Couleur du câble   |
| -----------------------  | ---------------------- | ------------: |
| VDD (broche 27A)            | 3V (broche 3A)            | Câble rouge     |
| GND (broche 29A)            | GND (broche 6A)           | Câble noir   |
| SCK (broche 30A)            | SCK (broche 12A)          | Câble jaune  |
| SDO (broche 31A)            | MI (broche 14A)           | Câble blanc   |
| SDI (broche 32A)            | M0 (broche 13A)           | Câble bleu    |
| CS (broche 33A)             | GPIO 5 (broche 15J)       | Câble orange  |

Pour plus d’informations, consultez [Adafruit BME280 Humidity + Barometric Pressure + Temperature Sensor Breakout](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) (Atelier sur le capteur de température, de pression barométrique et d’humidité Adafruit BME280) et [Adafruit Feather M0 WiFi Pinouts](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts) (Brochages de la carte Adafruit Feather M0 WiFi).



Votre carte Feather M0 WiFi devrait à présent être connectée à un capteur opérationnel.

![Connexion du capteur DHT22 à la carte Feather Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Connexion de la carte Feather M0 WiFi à votre ordinateur

Utilisez le câble Micro USB vers USB Type A pour connecter la carte Feather M0 WiFi à votre ordinateur, comme illustré ci-dessous :

![Connexion de la carte Feather Huzzah à votre ordinateur](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Ajouter des autorisations de port série (Ubuntu uniquement)

Si vous utilisez Ubuntu, assurez-vous que vous disposez des autorisations nécessaires pour utiliser le port USB de la carte Feather M0 WiFi. Pour ajouter des autorisations de port série, procédez comme suit :


1. Dans un terminal, exécutez les commandes suivantes :

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Vous obtenez l’une des sorties suivantes :

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Dans la sortie, `uucp` ou `dialout` correspond au nom du propriétaire du groupe du port USB.

2. Pour ajouter l’utilisateur au groupe, exécutez la commande suivante :

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   À l’étape précédente, vous avez obtenu le nom du propriétaire du groupe `<group-owner-name>`. Votre nom d’utilisateur Ubuntu est `<username>`.

3. Pour que la modification prenne effet, déconnectez-vous d’Ubuntu, puis reconnectez-vous.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Collecter les données du capteur et les envoyer à votre IoT Hub

Dans cette section, vous allez déployer et exécuter un exemple d’application sur la carte Feather M0 WiFi. L’exemple d’application fait clignoter la LED de la carte Feather M0 WiFi. Il envoie ensuite les données de température et d’humidité collectées à partir du capteur BME280 à votre IoT Hub.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>Obtenir l’exemple d’application à partir de GitHub et préparer l’IDE Arduino

L’exemple d’application est hébergé sur GitHub. Clonez l’exemple de référentiel contenant l’exemple d’application à partir de GitHub. Pour cloner l’exemple de référentiel, procédez comme suit :

1. Ouvrez une invite de commandes ou une fenêtre de terminal.

2. Accédez au dossier à utiliser pour le stockage de l’exemple d’application.
3. Exécutez la commande suivante :

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Installer le package pour la carte Feather M0 WiFi dans l’IDE Arduino

1. Ouvrez le dossier où l’exemple d’application est stocké.

2. Ouvrez le fichier app.ino dans le dossier app de l’IDE Arduino.

   ![Ouverture de l’exemple d’application dans l’IDE Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Cliquez sur **Fichier** > **Préférences** (Windows/Linux) ou sur **Arduino** > **Préférences** (Mac), puis copiez et collez le lien ci-dessous dans le champ de l’option **URL de gestionnaire de cartes supplémentaires**.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Cliquez sur **Outils** > **Carte** > **Gestionnaire de cartes**, puis installez `Arduino SAMD Boards` version `1.6.2` ou ultérieure. 

1. Dans la même fenêtre, installez ensuite le package `Adafruit SAMD Boards` pour ajouter les définitions de fichier de carte.

   ![Le package ESP8266 est installé](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Cliquez sur **Outils** > **Carte** > **Adafruit M0 WiFi**.

5. Installez les pilotes (pour Windows uniquement). Lorsque vous branchez la carte M0 WiFi, il se peut que vous deviez installer un pilote. Cliquez sur [ce lien](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) pour télécharger le programme d’installation des pilotes. Suivez les étapes à l’écran pour installer les pilotes souhaités.

### <a name="install-necessary-libraries"></a>Installer les bibliothèques nécessaires

1. Dans l’IDE Arduino, cliquez sur **Croquis** > **Inclure une bibliothèque** > **Gérer les bibliothèques**.

2. Recherchez les noms de bibliothèque suivants un par un. Pour chacune des bibliothèques trouvées, cliquez sur **Installer** :

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Installez manuellement `Adafruit_WINC1500`. Accédez à [ce site web](https://github.com/adafruit/Adafruit_WINC1500) et cliquez sur **Clone or download** (Cloner ou télécharger)  > **Download ZIP** (Télécharger ZIP). Ensuite, dans votre IDE Arduino, accédez à **Croquis** > **Inclure une bibliothèque** > **Ajouter la bibliothèque .ZIP** et ajoutez le fichier zip.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>Utiliser l’exemple d’application en l’absence de capteur BME280

Si vous n’avez pas de capteur BME280, l’exemple d’application permet de simuler des données de température et d’humidité. Pour que l’exemple d’application utilise des données simulées, procédez comme suit :

1. Ouvrez le fichier `config.h` dans le dossier `app`.

2. Recherchez la ligne de code suivante et remplacez la valeur `false` par `true` :

   ```c
   define SIMULATED_DATA true
   ```
   ![Configuration de l’exemple d’application pour utiliser des données simulées](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Enregistrez le fichier avec `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Déployer l’exemple d’application sur la carte M0 WiFi

1. Dans l’IDE Arduino, cliquez sur **Outil** > **Port**, puis cliquez sur le port série pour la carte Feather M0 WiFi.

2. Cliquez sur **Croquis** > **Téléverser** pour générer et déployer l’exemple d’application sur la carte Feather M0 WiFi.

### <a name="enter-your-credentials"></a>Entrer vos informations d’identification

Une fois le chargement terminé, suivez cette procédure pour entrer vos informations d’identification :

1. Dans l’IDE Arduino, cliquez sur **Outils** > **Moniteur série**.

2. Dans le coin inférieur droit de la fenêtre du moniteur série, sélectionnez **Pas de fin de ligne** dans la liste déroulante de gauche.
3. Dans la liste déroulante de droite, sélectionnez **115200 baud**.
4. Dans la zone de saisie située en haut, entrez les informations suivantes si vous êtes invité à les fournir, puis cliquez sur **Envoyer** :

   * SSID Wi-Fi
   * Mot de passe Wi-Fi
   * Chaîne de connexion de l’appareil

> [!Note]
> Les informations d’identification sont stockées dans la mémoire EEPROM de la carte Feather M0 WiFi. Si vous cliquez sur le bouton de réinitialisation de la carte Feather M0 WiFi, l’exemple d’application vous demande si vous souhaitez effacer ces informations. Entrez `Y` pour effacer les informations. Vous êtes invité à fournir les informations une deuxième fois.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Vérifier que l’exemple d’application s’exécute correctement

Si vous voyez la sortie suivante dans la fenêtre du moniteur série et la LED clignoter sur la carte Feather M0 WiFi, l’exemple d’application s’exécute correctement :

![Sortie finale dans l’IDE Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement connecté la carte Feather M0 WiFi à votre IoT Hub et envoyé les données de capteur collectées à votre IoT Hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


