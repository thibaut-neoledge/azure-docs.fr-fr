---
title: "IoT DevKit vers le cloud - Connecter IoT DevKit AZ3166 à Azure IoT Hub | Microsoft Docs"
description: "Dans ce didacticiel, découvrez comment configurer et connecter IoT DevKit AZ3166 à Azure IoT Hub pour lui permettre d’envoyer des données à la plateforme cloud Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 122fac584ac5b954ef7b33a3121bee2c502ebc63
ms.contentlocale: fr-fr
ms.lasthandoff: 08/16/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Connecter IoT DevKit AZ3166 à Azure IoT Hub dans le cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) permet de développer et de prototyper des solutions de l’Internet des objets (IoT) exploitant les services Microsoft Azure. Il comprend une carte compatible avec Arduino et équipée d’un large éventail de périphériques et de capteurs, un package de carte open source et un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) grandissant.

## <a name="what-you-do"></a>Procédure
Connectez [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) au hub Azure IoT que vous avez créé, collectez les données de température et d’humidité et envoyez-les à IoT Hub.

Vous n’avez pas encore de DevKit ? Cliquez [ici](https://aka.ms/iot-devkit-purchase) pour en obtenir un.

## <a name="what-you-learn"></a>Contenu

* Comment connecter IoT DevKit à un point d’accès sans fil et préparer votre environnement de développement.
* Comment créer un hub IoT et enregistrer un appareil auprès de MXChip IoT DevKit.
* Comment collecter les données des capteurs en exécutant un exemple d’application sur MXChip IoT DevKit.
* Envoi des données du capteur à votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

* Une carte MXChip IoT DevKit avec câble micro USB. [Cliquez ici pour l’obtenir](https://aka.ms/iot-devkit-purchase).
* Un ordinateur sous Windows 10 ou macOS 10.10 ou une version ultérieure.
* Un abonnement Azure actif
  * Activez un [compte d’évaluation Microsoft Azure gratuit pendant 30 jours](https://azureinfo.microsoft.com/us-freetrial.html).

## <a name="prepare-your-hardware"></a>Préparation du matériel

Raccordez le matériel à votre ordinateur.

### <a name="hardware-you-need"></a>Matériel nécessaire

* Carte DevKit
* Câble micro USB

![bien-démarrer-matériel](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

### <a name="connect-devkit-to-your-computer"></a>Connecter DevKit à votre ordinateur

1. Connectez la fiche USB à votre PC.
2. Connectez la fiche micro USB à DevKit.
3. La DEL verte à côté de « Power » confirme la connexion.

![bien-démarrer-connexion](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wifi"></a>Configurer le WiFi

Les projets IoT dépendent de la connectivité à Internet. Utilisez les instructions suivantes pour configurer DevKit afin qu’il se connecte au WiFi.

### <a name="enter-ap-mode"></a>Activer le mode « point d’accès »

Maintenez le bouton B enfoncé, puis appuyez sur le bouton de réinitialisation et relâchez-le, et enfin relâchez le bouton B. Votre DevKit passe en mode « point d’accès » pour la configuration du WiFi. L’écran affiche l’identificateur SSID (Service Set Identifier) du DevKit, ainsi que l’adresse IP du portail de configuration :

![bien-démarrer-point-d-accès-wifi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Se connecter au point d’accès DevKit

Utilisez à présent un autre appareil compatible WiFi (téléphone mobile ou PC) pour vous connecter au SSID DevKit (mis en surbrillance dans la capture d’écran ci-dessus) ; laissez le mot de passe vide.

![bien-démarrer-ssid](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wifi-for-devkit"></a>Configurer le WiFi pour DevKit

Ouvrez l’adresse IP qui s’affiche sur l’écran DevKit du navigateur de votre PC ou de votre téléphone mobile, sélectionnez le réseau WiFi auquel vous souhaitez que le DevKit se connecte, puis tapez le mot de passe. Cliquez sur **Se connecter** pour terminer :

![bien-démarrer-portail-wifi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Une fois la connexion établie, le DevKit redémarre après quelques secondes. En cas de réussite, l’adresse IP et le nom du WiFi apparaissent à l’écran :

![bien-démarrer-ip-wifi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
L’adresse IP présentée sur la photo peut ne pas correspond à l’adresse IP réellement attribuée et affichée sur l’écran du DevKit. Cette divergence est normale, dans la mesure où le WiFi utilise DHCP pour attribuer dynamiquement les adresses IP.

Après la configuration du WiFi, vos informations d’identification sont persistantes pour cette connexion sur l’appareil, même s’il est débranché. Par exemple, si vous avez configuré le DevKit pour le WiFi à votre domicile, puis que vous l’avez emporté au bureau, vous devrez reconfigurer le mode « point d’accès » (en commençant à l’étape **Activer le mode « point d’accès »**) pour connecter le DevKit au WiFi de votre bureau. 

## <a name="start-using-devkit"></a>Commencer à utiliser DevKit

L’application qui s’exécute par défaut sur DevKit vérifie la dernière version du microprogramme et vous présente certaines données de diagnostic des capteurs.

### <a name="upgrade-to-the-latest-firmware"></a>Passer à la dernière version du microprogramme

Si une mise à niveau est nécessaire, la version actuelle et la dernière version du microprogramme s’affichent à l’écran. Suivez le guide [Mettre à niveau le microprogramme](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) pour le mettre à niveau.

![bien-démarrer-microprogramme](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
Vous n’aurez pas besoin de suivre à nouveau cette procédure : quand vous commencerez à développer sur le DevKit et à charger votre application, la dernière version du microprogramme sera fournie avec votre application.

### <a name="test-various-sensors"></a>Tester divers capteurs

Appuyez sur le bouton B pour tester les capteurs, puis appuyez à nouveau et relâchez-le pour passer en revue les capteurs.

![bien-démarrer-capteurs](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-development-environment"></a>Préparer l’environnement de développement

Il s’agit maintenant de configurer l’environnement de développement, c’est-à-dire les outils et les packages qui vous permettront de créer de superbes applications IoT. Vous pouvez choisir la version Windows ou macOS en fonction de votre système d’exploitation.


### <a name="windows"></a>Windows

Nous vous encourageons à utiliser le package d’installation pour préparer l’environnement de développement. Si vous rencontrez des problèmes, vous pouvez pour cela suivre les [étapes manuelles](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/).

#### <a name="download-latest-package"></a>Télécharger la dernière version du package

Le fichier `.zip` que vous téléchargez contient tous les outils et packages nécessaires pour le développement DevKit.

> [!div class="button"]
[Télécharger](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


> [!NOTE] 
> Le fichier `.zip` contient les outils et les packages suivants. Si certains composants sont déjà installés, le script les détecte et les ignore.
> * Node.js et Yarn : runtime pour le script d’installation et les tâches automatisées
> * [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows) : expérience multiplateforme en ligne de commande pour la gestion des ressources Azure ; le fichier MSI contient les modules Python et pip dépendants.
> * [Visual Studio Code](https://code.visualstudio.com/) : éditeur de code léger pour le développement DevKit
> * [Extension Visual Studio Code pour Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino) : permet le développement Arduino dans VS Code
> * [IDE Arduino](https://www.arduino.cc/en/Main/Software): l’extension pour Arduino s’appuie sur cet outil
> * Package de la carte DevKit : chaînes d’outils, bibliothèques et projets pour le DevKit
> * Utilitaire ST-Link : pilotes et utilitaires essentiels

#### <a name="run-installation-script"></a>Exécuter le script d’installation

Dans l’Explorateur de fichiers Windows, localisez le `.zip` et extrayez-le, recherchez `install.cmd`, cliquez avec le bouton droit et sélectionnez **« Exécuter en tant qu’administrateur »** pour commencer.

![bien-démarrer-exécution-administrateur](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Pendant l’installation, vous verrez la progression de chaque outil ou package.

![bien-démarrer-installation](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="confirm-to-install-drivers"></a>Confirmer pour installer les pilotes

L’extension VS Code pour Arduino s’appuie sur l’IDE Arduino. Si c’est la première fois que vous installez l’IDE Arduino, vous devrez installer les pilotes correspondants :

![bien-démarrer-pilote](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

L’installation devrait prendre environ 10 minutes, en fonction de la vitesse de votre connexion Internet. Après l’installation, les raccourcis Visual Studio Code et IDE Arduino apparaissent sur votre bureau.

> [!NOTE] 
Au lancement de VS Code, il peut arriver qu’une erreur s’affiche, indiquant que l’IDE Arduino ou un package de carte associé est introuvable. Pour la résoudre, fermez VS Code et lancez l’IDE Arduino une seule fois ; VS Code devrait localiser correctement le chemin d’accès de l’IDE Arduino.


### <a name="macos-preview"></a>macOS (préversion)

Suivez ces étapes pour préparer l’environnement de développement sous macOS.

#### <a name="install-azure-cli-20"></a>Installer Azure CLI 2.0

Suivez les [guide officiel](https://docs.microsoft.com//cli/azure/install-azure-cli) pour installer Azure CLI 2.0 :

Installez Azure CLI 2.0 avec une commande `curl` :

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

Et redémarrez votre interface de commande pour que les modifications soient prises en compte :

```bash
exec -l $SHELL
```

#### <a name="install-arduino-ide"></a>Installer l’IDE Arduino

L’extension Arduino Visual Studio Code s’appuie sur l’IDE Arduino. Téléchargez et installez [l’IDE Arduino pour macOS](https://www.arduino.cc/en/Main/Software).

#### <a name="install-visual-studio-code"></a>Installation de Visual Studio Code

Téléchargez et installez [Visual Studio Code pour macOS](https://code.visualstudio.com/). Ce sera l’outil de développement principal pour créer des applications IoT DevKit.

####  <a name="download-latest-package"></a>Télécharger la dernière version du package

1. Installez Node.js. Vous pouvez utiliser le Gestionnaire de package macOS courant nommé [Homebrew](https://brew.sh/) ou le [programme d’installation préintégré](https://nodejs.org/en/download/) pour l’installer.

2. Téléchargez le fichier `.zip` contenant les scripts de tâches requis pour le développement DevKit dans VS Code.

   > [!div class="button"]
   [Télécharger](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

   Recherchez le `.zip` et extrayez-le. Puis lancez l’application **Terminal** et exécutez les commandes suivantes pour effectuer la configuration :

   Déplacez le dossier extrait dans votre dossier d’utilisateur macOS :
   ```bash
   mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
   ```
  
   Installez les packages `npm` :
   ```
   npm install
   ```

#### <a name="install-vs-code-extension-for-arduino"></a>Installer l’extension VS Code pour Arduino

Visual Studio Code permet d’installer les extensions Marketplace directement dans l’outil. Cliquez simplement sur l’icône des extensions dans le volet du menu gauche, puis recherchez `Arduino` pour l’installer :

![installation-extensions](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-devkit-board-package"></a>Installer le package de carte DevKit

Vous devrez ajouter la carte DevKit à l’aide du Gestionnaire de carte de Visual Studio Code.

1. Utilisez `Cmd+Shift+P` pour appeler la palette de commandes et tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Gestionnaire de carte**.

2. Cliquez sur **« URL supplémentaires »** en bas à droite.
   ![installation-url-supplémentaires](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. Dans le fichier `settings.json`, ajoutez une ligne en bas du volet `USER SETTINGS` et enregistrez-la.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![installation-paramètres-json](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. Maintenant, dans le Gestionnaire de carte, recherchez « az3166 » et installez la dernière version.
   ![installation-az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

Tous les outils et packages nécessaires sont à présent installés pour macOS.


## <a name="open-project-folder"></a>Ouvrir un dossier de projet

### <a name="launch-vs-code"></a>Lancer VS Code

Assurez-vous que votre DevKit n’est pas connecté. Lancez d’abord VS Code, puis connectez le DevKit à votre ordinateur. VS Code le détecte automatiquement et ouvre une page d’introduction :

![mini-solution-vscode](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
Au lancement de VS Code, il peut arriver qu’une erreur s’affiche, indiquant que l’IDE Arduino ou un package de carte associé est introuvable. Pour la résoudre, fermez VS Code et relancez l’IDE Arduino ; VS Code devrait localiser correctement le chemin d’accès de l’IDE Arduino.

### <a name="open-arduino-examples-folder"></a>Ouvrir le dossier des exemples Arduino

Basculez vers l’onglet **« Exemples Arduino »**, accédez à `Examples for MXCHIP AZ3166 > AzureIoT` et cliquez sur `GetStarted`.

![mini-solution-exemples](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Si vous fermez le volet par inadvertance et que vous souhaitez le recharger, utilisez `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour appeler une palette de commandes et tapez **Arduino** pour rechercher et sélectionner **Arduino : exemples**.

## <a name="provision-azure-services"></a>Approvisionner les services Azure

Dans la fenêtre de la solution, exécutez votre tâche avec `Ctrl+P` (macOS : `Cmd+P`) en tapant « task cloud-provision » :

Dans le terminal VS Code, une ligne de commande interactive vous guide dans l’approvisionnement des services Azure nécessaires :

![mini-solution-approvisionnement-cloud](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-arduino-sketch"></a>Générer et charger un croquis Arduino

### <a name="install-required-library"></a>Installer la bibliothèque requise

1. Appuyez sur `F1` ou `Ctrl+Shift+P` (macOS : `Cmd+Shift+P`) pour appeler la palette de commandes et tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Gestionnaire de bibliothèque**.

2. Recherchez la bibliothèque `ArduinoJson` et cliquez sur **Installer**.

### <a name="build-and-upload-the-device-code"></a>Générer et charger le code de l’appareil

Utilisez `Ctrl+P` (macOS : `Cmd+P`) pour exécuter « task device-upload ». Le terminal vous invite à passer au mode de configuration. Pour cela, maintenez le bouton A enfoncé, puis appuyez sur le bouton de réinitialisation et relâchez-le. L’écran affiche « Configuration ». Cela permet de définir la chaîne de connexion qui récupère à partir de l’étape de « task cloud-provision ».

La vérification et le chargement du croquis Arduino commence ensuite :

![mini-solution-chargement-appareil](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Le DevKit redémarre et commence à exécuter le code.

## <a name="test-the-project"></a>Tester le projet

Dans VS Code, cliquez sur l’icône de branchement « power » dans la barre d’état pour ouvrir l’analyse en série.

L’exemple d’application s’exécute correctement si les résultats suivants s’affichent :

* L’analyse en série affiche les mêmes informations que dans la capture d’écran ci-dessous.
* La LED de MXChip IoT DevKit clignote.

![Sortie finale dans VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Vous pouvez consulter les [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) si vous rencontrez des problèmes, ou nous contacter par les canaux ci-dessous.

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement connecté un MXChip IoT DevKit à votre hub IoT et envoyé les données de capteur collectées à votre hub IoT.

Pour continuer la prise en main de IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

- [Gérer la messagerie de périphérique cloud avec iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Enregistrer les messages IoT Hub dans le stockage de données Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Utiliser Power BI pour visualiser des données de capteur en temps réel provenant d’Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Utiliser Azure Web Apps pour visualiser des données de capteur en temps réel provenant d’Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Prévision météo utilisant les données de capteur de votre IoT Hub dans Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Gestion des appareils avec iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Surveillance à distance et notifications avec Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
