---
title: "ESP8266 vers cloud - Connecter la carte Sparkfun ESP8266 Thing Dev à Azure IoT Hub | Microsoft Docs"
description: "Dans ce didacticiel, découvrez comment configurer et connecter Sparkfun ESP8266 Thing Dev à Azure IoT Hub pour lui permettre d’envoyer des données à la plateforme cloud Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.openlocfilehash: 557f0cdf375b345e0dbe0526f5a5bd3c050dec38
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2017
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Connecter la carte Sparkfun ESP8266 Thing Dev à Azure IoT Hub dans le cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![connexion entre un capteur DHT22, Thing Dev et IoT Hub](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Procédure à suivre

Connectez la carte Sparkfun ESP8266 Thing Dev à une instance Azure IoT Hub que vous allez créer. Ensuite, vous exécuterez un exemple d’application sur l’ESP8266 pour collecter des données de température et d’humidité provenant d’un capteur DHT22. Enfin, vous enverrez les données du capteur à votre IoT Hub.

> [!NOTE]
> Si vous utilisez une autre carte ESP8266, vous pouvez également suivre ces étapes pour la connecter à votre IoT Hub. Selon la carte ESP8266 que vous utilisez, il se peut que vous deviez reconfigurer le paramètre `LED_PIN`. Par exemple, si vous utilisez l’ESP8266 d’AI-Thinker, vous devrez peut-être remplacer la valeur `0` de ce paramètre par `2`. Vous n’avez pas encore de kit ? Cliquez [ici](http://azure.com/iotstarterkits).

## <a name="what-you-will-learn"></a>Contenu

* Création d’une instance IoT Hub et enregistrement d’un appareil pour la carte Thing Dev.
* Connexion de la carte Thing Dev au capteur et à votre ordinateur.
* Collecte des données du capteur via l’exécution d’un exemple d’application sur une carte Thing Dev.
* Envoi des données du capteur à votre IoT Hub.

## <a name="what-you-will-need"></a>Éléments requis

![Composants requis pour le didacticiel](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Pour effectuer cette opération, vous aurez besoin des composants suivants de votre Kit de démarrage Thing Dev :

* Une carte Sparkfun ESP8266 Thing Dev.
* Un câble Micro USB Micro B vers USB Type A.

Vous aurez également besoin des éléments suivants pour votre environnement de développement :

* Un abonnement Azure actif. Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation Azure gratuit](https://azure.microsoft.com/free/) en quelques minutes.
* Un Mac ou un PC exécutant Windows ou Ubuntu.
* Un réseau sans fil pour la connexion de la carte Sparkfun ESP8266 Thing Dev.
* Une connexion Internet pour télécharger l’outil de configuration.
* [L’IDE Arduino](https://www.arduino.cc/en/main/software) version 1.6.8 ou ultérieure (les versions antérieures ne fonctionneront pas avec la bibliothèque AzureIoT).

Les éléments suivants sont facultatifs si vous n’avez pas de capteur. Vous avez également la possibilité d’utiliser des données de capteur simulées.

* Un capteur de température et d’humidité Adafruit DHT22.
* Une platine d’expérimentation.
* Des câbles de liaison M/M.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Connecter la carte ESP8266 Thing Dev au capteur et à votre ordinateur

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Connecter un capteur de température et d’humidité DHT22 à la carte ESP8266 Thing Dev

Utilisez la platine d’expérimentation et les câbles de liaison pour effectuer la connexion comme suit. Si vous n’avez pas de capteur, ignorez cette section. Vous pourrez utiliser des données de capteur simulées à la place.

![Schéma des connexions](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Pour les broches du capteur, nous utiliserons le câblage suivant :

| Début (capteur)           | Fin (carte)           | Couleur du câble   |
| -----------------------  | ---------------------- | ------------: |
| VDD (broche 27F)            | 3V (broche 8A)           | Câble rouge     |
| DATA (broche 28F)           | GPIO 2 (broche 9A)       | Câble blanc    |
| GND (broche 30F)            | GND (broche 7J)          | Câble noir   |


- Pour en savoir plus, consultez les [détails de la configuration du capteur DHT22](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) et les [spécifications relatives à la carte Sparkfun ESP8266 Thing Dev](https://www.sparkfun.com/products/13711).

Votre carte Sparkfun ESP8266 Thing Dev devrait à présent être connectée à un capteur opérationnel.

![connecter un capteur dht22 à la carte ESP8266 Thing Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Connecter la carte Sparkfun ESP8266 Thing Dev à votre ordinateur

À l’aide du câble Micro USB vers USB Type A, connectez la carte Sparkfun ESP8266 Thing Dev à votre ordinateur en procédant comme suit.

![Connexion de la carte Feather HUZZAH à votre ordinateur](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Ajouter des autorisations de port série (Ubuntu uniquement)

Si vous utilisez Ubuntu, assurez-vous qu’un utilisateur normal dispose des autorisations nécessaires pour utiliser le port USB de la carte Sparkfun ESP8266 Thing Dev. Pour ajouter des autorisations de port série pour un utilisateur normal, procédez comme suit :

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

1. Déconnectez-vous de Ubuntu et connectez-vous à nouveau pour que la modification prenne effet.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Collecter les données du capteur et les envoyer à votre IoT Hub

Dans cette section, vous allez déployer et exécuter un exemple d’application sur la carte Sparkfun ESP8266 Thing Dev. L’exemple d’application fait clignoter la LED de la carte Sparkfun ESP8266 Thing Dev et envoie les données de température et d’humidité collectées à partir du capteur DHT22 à votre instance IoT Hub.

### <a name="get-the-sample-application-from-github"></a>Obtenir l’exemple d’application à partir de GitHub

L’exemple d’application est hébergé sur GitHub. Clonez l’exemple de référentiel contenant l’exemple d’application à partir de GitHub. Pour cloner l’exemple de référentiel, procédez comme suit :

1. Ouvrez une invite de commandes ou une fenêtre de terminal.
1. Accédez au dossier à utiliser pour le stockage de l’exemple d’application.
1. Exécutez la commande suivante :

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Installez le package de la carte Sparkfun ESP8266 Thing Dev dans l’IDE Arduino :

1. Ouvrez le dossier où l’exemple d’application est stocké.
1. Ouvrez le fichier app.ino dans le dossier app de l’IDE Arduino.

   ![Ouverture de l’exemple d’application dans l’IDE Arduino](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. Dans l’IDE Arduino, cliquez sur **Fichier** > **Préférences**.
1. Dans la boîte de dialogue **Préférences**, cliquez sur l’icône en regard de la zone de texte **URL de gestionnaire de cartes supplémentaires**.
1. Dans la fenêtre contextuelle, entrez l’URL suivante, puis cliquez sur **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Pointage vers l’URL d’un package dans l’IDE Arduino](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. Dans la boîte de dialogue **Préférences**, cliquez sur **OK**.
1. Cliquez sur **Outils** > **Type de carte** > **Gestionnaire de carte**, puis recherchez « esp8266 ».
   La version 2.2.0 ou ultérieure du package ESP8266 doit être installée.

   ![Le package ESP8266 est installé](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Cliquez sur **Outils** > **Carte** > **Sparkfun ESP8266 Thing Dev**.

### <a name="install-necessary-libraries"></a>Installer les bibliothèques nécessaires

1. Dans l’IDE Arduino, cliquez sur **Croquis** > **Inclure une bibliothèque** > **Gérer les bibliothèques**.
1. Recherchez les noms de bibliothèque suivants un par un. Pour chacune des bibliothèques trouvées, cliquez sur **Installer**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Vous n’avez pas de capteur DHT22 ?

L’exemple d’application permet de simuler des données de température et d’humidité si vous n’avez pas de capteur DHT22. Pour activer l’utilisation de données simulées par l’exemple d’application, procédez comme suit :

1. Ouvrez le fichier `config.h` dans le dossier `app`.
1. Recherchez la ligne de code suivante et remplacez la valeur `false` par `true` :
   ```c
   define SIMULATED_DATA true
   ```
   ![Configuration de l’exemple d’application pour utiliser des données simulées](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Enregistrez avec `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Déployer l’exemple d’application sur la carte Sparkfun ESP8266 Thing Dev

1. Dans l’IDE Arduino, cliquez sur **Outil** > **Port**, puis sélectionnez le port série de la carte Sparkfun ESP8266 Thing Dev.
1. Cliquez sur **Croquis** > **Téléverser** pour générer et déployer l’exemple d’application sur la carte Sparkfun ESP8266 Thing Dev.

> [!Note]
> Si vous utilisez macOS, vous avez probablement pu voir les messages suivants lors du chargement. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Ouvrez la fenêtre du terminal et terminez les actions ci-dessous pour résoudre ce problème.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Entrer vos informations d’identification

Une fois le chargement terminé, suivez cette procédure pour entrer vos informations d’identification :

1. Dans l’IDE Arduino, cliquez sur **Outils** > **Moniteur série**.
1. Dans la fenêtre Moniteur série, vous pouvez remarquer la présence de deux listes déroulantes dans l’angle inférieur droit.
1. Pour la liste déroulante de gauche, sélectionnez **Pas de fin de ligne**.
1. Pour la liste déroulante de droite, sélectionnez **115200 baud**.
1. Dans la zone de saisie située en haut de la fenêtre Moniteur série, entrez les informations suivantes si elles vous sont demandées, puis cliquez sur **Envoyer**.
   * SSID Wi-Fi
   * Mot de passe Wi-Fi
   * Chaîne de connexion de l’appareil

> [!Note]
> Les informations d’identification sont stockées dans la mémoire EEPROM de la carte Sparkfun ESP8266 Thing Dev. Si vous cliquez sur le bouton de réinitialisation de la carte Sparkfun ESP8266 Thing Dev, l’exemple d’application vous demande si vous souhaitez effacer ces informations. Entrez `Y` pour les effacer. Vous serez alors à nouveau invité à fournir ces informations.

### <a name="verify-the-sample-application-is-running-successfully"></a>Vérifier que l’exemple d’application s’exécute correctement

Si vous voyez la sortie suivante dans la fenêtre Moniteur série, et si la LED clignote sur la carte Sparkfun ESP8266 Thing Dev, cela signifie que l’exemple d’application s’exécute correctement.

![Sortie finale dans l’IDE Arduino](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement connecté une carte Sparkfun ESP8266 Thing Dev à votre instance IoT Hub et envoyé les données de capteur collectées à cette instance. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
