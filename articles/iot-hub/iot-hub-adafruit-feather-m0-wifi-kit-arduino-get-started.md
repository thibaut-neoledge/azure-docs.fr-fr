---
title: "M0 vers cloud - Connecter Feather M0 WiFi à Azure IoT Hub | Microsoft Docs"
description: "Explique comment connecter un appareil Arduino, nommé Adafruit Feather M0 WiFi, à Azure IoT Hub, un service cloud Microsoft qui vous aide à gérer vos ressources IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/17/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 2f770a8f088754e63aec40d3f670f6ae0543d6e0
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017


---

# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Connecter Adafruit Feather M0 WiFi à Azure IoT Hub dans le cloud
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Connexion entre BME280, Feather M0 WiFi et IoT Hub](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

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
* [Arduino IDE](https://www.arduino.cc/en/main/software) version 1.6.8 ou ultérieure. Les versions antérieures ne fonctionneront pas avec la bibliothèque AzureIoT.


Les éléments suivants sont facultatifs si vous n’avez pas de capteur. Vous avez également la possibilité d’utiliser des données de capteur simulées.

* Un capteur de température et d’humidité BME280
* Une platine d’expérimentation
* Des câbles de liaison M/M

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Connecter la carte Feather M0 WiFi au capteur et à votre ordinateur
Dans cette section, vous connectez les capteurs à votre carte. Puis vous connectez votre appareil à votre ordinateur pour une utilisation ultérieure.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Connecter un capteur de température et d’humidité DHT22 à la carte Feather M0 WiFi

Utilisez la platine d’expérimentation et les câbles de liaison pour effectuer la connexion comme suit. Si vous n’avez pas de capteur, ignorez cette section. Vous pourrez utiliser des données de capteur simulées à la place.

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

Comme indiqué ci-après, utilisez le câble Micro USB vers USB Type A pour connecter la carte Feather M0 WiFi à votre ordinateur.

![Connexion de la carte Feather Huzzah à votre ordinateur](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Ajouter des autorisations de port série (Ubuntu uniquement)

Si vous utilisez Ubuntu, assurez-vous que vous disposez des autorisations nécessaires pour utiliser le port USB de la carte Feather M0 WiFi. Pour ajouter des autorisations de port série, procédez comme suit :


1. Dans un terminal, exécutez les commandes suivantes :

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Vous obtenez l’une des sorties suivantes :

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Dans la sortie, `uucp` ou `dialout` correspond au nom du propriétaire du groupe du port USB.

1. Ajoutez l’utilisateur au groupe en exécutant la commande suivante :

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` est le nom du propriétaire du groupe que vous avez obtenu à l’étape précédente. `<username>` est le nom de votre utilisateur Ubuntu.

1. Déconnectez-vous d’Ubuntu, puis reconnectez-vous pour que la modification prenne effet.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Collecter les données du capteur et les envoyer à votre IoT Hub

Dans cette section, vous allez déployer et exécuter un exemple d’application sur la carte Feather M0 WiFi. L’exemple d’application fait clignoter la LED de la carte Feather M0 WiFi et envoie les données de température et d’humidité collectées à partir du capteur BME280 à votre IoT Hub.

### <a name="get-the-sample-application-from-github-and-prepare-arduino-ide"></a>Obtenir l’exemple d’application à partir de GitHub et préparer Arduino IDE

L’exemple d’application est hébergé sur GitHub. Clonez l’exemple de référentiel contenant l’exemple d’application à partir de GitHub. Pour cloner l’exemple de référentiel, procédez comme suit :

1. Ouvrez une invite de commandes ou une fenêtre de terminal.
1. Accédez au dossier à utiliser pour le stockage de l’exemple d’application.
1. Exécutez la commande suivante :

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

Installez le package pour la carte Feather M0 WiFi dans l’IDE Arduino :

1. Ouvrez le dossier où l’exemple d’application est stocké.
1. Ouvrez le fichier app.ino dans le dossier app de l’IDE Arduino.

   ![Ouverture de l’exemple d’application dans l’IDE Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)

1. Cliquez sur **Outils** > **Carte** > **Gestionnaire de cartes**, puis installez `Arduino SAMD Boards` version `1.6.2` ou ultérieure. Installez ensuite le package `Adafruit SAMD` pour ajouter les définitions de fichier de carte.

   Le gestionnaire de cartes indique que `Arduino SAMD Boards` est installé avec une version `1.6.2` ou ultérieure. 

   ![Le package ESP8266 est installé](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

1. Cliquez sur **Outils** > **Carte** > **Adafruit M0 WiFi**.

1. Installer des pilotes (Windows uniquement) Lorsque vous branchez la carte Feather, vous devrez peut-être installer un pilote. Cliquez [ici](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) pour télécharger le programme d’installation du pilote.
   Suivez la procédure pour installer les pilotes souhaités.

### <a name="install-necessary-libraries"></a>Installer les bibliothèques nécessaires

1. Dans l’IDE Arduino, cliquez sur **Croquis** > **Inclure une bibliothèque** > **Gérer les bibliothèques**.
1. Recherchez les noms de bibliothèque suivants un par un. Pour chacune des bibliothèques trouvées, cliquez sur **Installer**.
   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`
1. Installez manuellement `Adafruit_WINC1500`. Suivez [ce lien](https://github.com/adafruit/Adafruit_WINC1500) et cliquez sur le bouton **Cloner ou télécharger**, puis **Download ZIP** (Télécharger le ZIP). Ensuite, dans votre IDE Arduino, accédez à **Croquis** -> **Include Library (Inclure une bibliothèque)** -> **Add .zip Library (Ajouter une bibliothèque .zip)** et ajoutez le fichier zip que vous venez de télécharger.

### <a name="dont-have-a-real-bme280-sensor"></a>Vous n’avez pas de capteur BME280 ?

L’exemple d’application permet de simuler des données de température et d’humidité si vous n’avez pas de capteur BME280. Pour que l’exemple d’application utilise des données simulées, procédez comme suit :

1. Ouvrez le fichier `config.h` dans le dossier `app`.
1. Recherchez la ligne de code suivante et remplacez la valeur `false` par `true` :
   ```c
   define SIMULATED_DATA true
   ```
   ![Configuration de l’exemple d’application pour utiliser des données simulées](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

1. Enregistrez le fichier avec `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Déployer l’exemple d’application sur la carte M0 WiFi

1. Dans l’IDE Arduino, cliquez sur **Outil** > **Port**, puis cliquez sur le port série pour la carte Feather M0 WiFi.
1. Cliquez sur **Croquis** > **Téléverser** pour générer et déployer l’exemple d’application sur la carte Feather M0 WiFi.

### <a name="enter-your-credentials"></a>Entrer vos informations d’identification

Une fois le chargement terminé, suivez cette procédure pour entrer vos informations d’identification :

1. Dans l’IDE Arduino, cliquez sur **Outils** > **Moniteur série**.
1. Dans la fenêtre Moniteur série, vous pouvez remarquer la présence de deux listes déroulantes dans le coin inférieur droit.
1. Pour la liste déroulante de gauche, sélectionnez **Pas de fin de ligne**.
1. Pour la liste déroulante de droite, sélectionnez **115200 baud**.
1. Dans la zone de saisie située en haut de la fenêtre Moniteur série, entrez les informations suivantes si elles vous sont demandées, puis cliquez sur **Envoyer**.
   * SSID Wi-Fi
   * Mot de passe Wi-Fi
   * Chaîne de connexion de l’appareil

> [!Note]
> Les informations d’identification sont stockées dans la mémoire EEPROM de la carte Feather M0 WiFi. Si vous cliquez sur le bouton de réinitialisation de la carte Feather M0 WiFi, l’exemple d’application vous demande si vous souhaitez effacer ces informations. Entrez `Y` pour effacer les informations. Vous êtes invité à fournir les informations une deuxième fois.

### <a name="verify-the-sample-application-is-running-successfully"></a>Vérifier que l’exemple d’application s’exécute correctement

Si vous voyez la sortie suivante dans la fenêtre Moniteur série et la LED clignoter sur la carte Feather M0 WiFi, l’exemple d’application s’exécute correctement.

![Sortie finale dans l’IDE Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement connecté une carte Feather M0 WiFi à votre IoT Hub et envoyé les données de capteur collectées à votre IoT Hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


