---
title: "Kit IoT DevKit dans le cloud - Connecter le kit IoT DevKit AZ3166 à Azure IoT Hub | Microsoft Docs"
description: "Dans ce didacticiel, découvrez comment configurer le kit IoT DevKit AZ3166 et le connecter à Azure IoT Hub pour qu’il puisse envoyer des données sur la plateforme cloud Azure."
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
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 253a2d34ea5b3f4f8253900be27c54fe5ca1dacf
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Connecter IoT DevKit AZ3166 à Azure IoT Hub dans le cloud

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Vous pouvez utiliser le kit [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) pour développer et réaliser des prototypes de solutions IoT (Internet of Things) qui tirent parti des services Microsoft Azure. Il comprend une carte compatible Arduino avec un grand choix de périphériques et de capteurs élaborés, un package de carte open source et un [catalogue de projets](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) de plus en plus riche.

## <a name="what-you-do"></a>Procédure
Connectez le kit [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) à un hub Azure IoT que vous créez, collectez les données de température et d’humidité à partir des capteurs et envoyez-les au hub IoT.

Vous n’avez pas encore de DevKit ? [Procurez-vous-en un](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Ce que vous allez apprendre

* Comment connecter le kit IoT DevKit à un point d’accès sans fil et préparer votre environnement de développement.
* Comment créer un hub IoT et enregistrer un appareil auprès du kit MXChip IoT DevKit.
* Comment collecter les données des capteurs en exécutant un exemple d’application sur le kit MXChip IoT DevKit.
* Envoi des données du capteur à votre hub IoT.

## <a name="what-you-need"></a>Ce dont vous avez besoin

* Une carte MXChip IoT DevKit avec câble micro-USB. [Achetez-en une maintenant](https://aka.ms/iot-devkit-purchase).
* Un ordinateur exécutant Windows 10 ou macOS 10.10+.
* Un abonnement Azure actif. [Activez un compte Microsoft Azure pour un essai gratuit de 30 jours](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Préparation du matériel

Raccordez le matériel à votre ordinateur.

Vous avez besoin du matériel suivant :

* Carte DevKit
* Câble micro-USB

![Matériel requis](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Pour brancher le kit DevKit sur votre ordinateur :

1. Branchez l’extrémité USB sur l’ordinateur.
2. Branchez la fiche micro-USB sur le kit DevKit.
3. Le témoin vert de l’alimentation confirme le branchement.

![Branchements du matériel](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Configurer le Wi-Fi

Les projets IoT sont tributaires de la connectivité Internet. Utilisez les instructions suivantes pour configurer la connexion du kit DevKit au Wi-Fi.

### <a name="enter-ap-mode"></a>Activer le mode « point d’accès »

Maintenez le bouton B enfoncé, appuyez sur le bouton de réinitialisation (Reset) et relâchez-le, puis relâchez le bouton B. Votre kit DevKit passe en mode « point d’accès » pour la configuration du Wi-Fi. L’écran affiche l’identificateur SSID (Service Set Identifier) du kit DevKit et l’adresse IP du portail de configuration.

![Bouton de réinitialisation, bouton B et SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Se connecter au point d’accès DevKit

Utilisez à présent un autre appareil compatible Wi-Fi (un téléphone mobile ou un ordinateur) pour vous connecter au SSID du kit DevKit (mis en évidence dans l’image précédente). Laissez le mot de passe vide.

![Bouton de connexion et informations réseau](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurer le Wi-Fi du kit DevKit

Ouvrez l’adresse IP affichée sur l’écran du kit DevKit dans le navigateur de votre ordinateur ou de votre téléphone mobile, sélectionnez le réseau Wi-Fi auquel vous souhaitez que le kit DevKit se connecte et tapez le mot de passe. Sélectionnez **Connecter**.

![Zone de mot de passe et bouton de connexion](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Dès que la connexion est établie, le kit DevKit redémarre au bout de quelques secondes. L’adresse IP et le nom du Wi-Fi apparaissent alors à l’écran :

![Nom Wi-Fi et adresse IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> L’adresse IP présentée sur la photo peut ne pas correspondre à l’adresse IP réellement attribuée et affichée sur l’écran du kit DevKit. Cette divergence est normale dans la mesure où le Wi-Fi utilise le DHCP pour attribuer dynamiquement les adresses IP.

Dès lors que le Wi-Fi est configuré, vos informations d’identification pour cette connexion sont persistantes sur l’appareil, même s’il est débranché. Par exemple, si vous configurez le kit DevKit pour le Wi-Fi à votre domicile, et que vous l’emportez ensuite au bureau, vous devez reconfigurer le mode « point d’accès » (en commençant à l’étape Activer le mode « point d’accès ») pour connecter le kit DevKit au Wi-Fi de votre bureau. 

## <a name="start-using-the-devkit"></a>Commencer à utiliser le kit DevKit

L’application qui s’exécute par défaut sur le kit DevKit vérifie la dernière version du microprogramme installée et affiche certaines données de diagnostic des capteurs.

### <a name="upgrade-to-the-latest-firmware"></a>Passer à la dernière version du microprogramme

Si une mise à niveau du microprogramme est nécessaire, l’écran affiche la version actuelle et la version la plus récente du microprogramme. Pour la mise à niveau, suivez le guide [Mettre à niveau le microprogramme](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/).

![Affichage de la versions actuelle et de la version plus récente du microprogramme](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Cette opération n’est nécessaire qu’une seule fois. Dès que vous commencez à développer sur le kit DevKit et que vous chargez votre application, la dernière version du microprogramme est fournie avec votre application.

### <a name="test-various-sensors"></a>Tester divers capteurs

Appuyez sur le bouton B pour tester les capteurs. Continuez d’appuyer sur ce bouton et de relâcher votre pression à chaque fois pour passer en revue tous les capteurs.

![Affichage d’un capteur et du bouton B](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Il s’agit maintenant de configurer l’environnement de développement, c’est-à-dire les outils et les packages qui vous permettront de créer de superbes applications IoT. Vous pouvez choisir la version Windows ou macOS en fonction de votre système d’exploitation.


### <a name="windows"></a>Windows

Nous vous encourageons à utiliser le package d’installation pour préparer l’environnement de développement. Si vous rencontrez des problèmes, vous pouvez suivre les [étapes de l’installation manuelle](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) pour y parvenir.

#### <a name="download-the-latest-package"></a>Télécharger le dernier package

Le fichier .zip que vous téléchargez contient tous les outils et packages nécessaires pour le développement DevKit.

> [!div class="button"]
[Télécharger](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


Le fichier .zip contient les outils et les packages suivants. Si certains composants sont déjà installés, le script les détecte et les ignore.

* Node.js et Yarn : runtime pour le script d’installation et les tâches automatisées.
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows) : expérience multiplateforme en ligne de commande pour la gestion des ressources Azure. Le fichier MSI contient les modules Python et pip dépendants.
* [Visual Studio Code](https://code.visualstudio.com/) (code VS) : éditeur de code léger pour le développement DevKit.
* [Extension Visual Studio Code pour Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino) : extension qui permet le développement Arduino dans Visual Studio Code.
* [IDE Arduino](https://www.arduino.cc/en/Main/Software) : outil sur lequel l’extension pour Arduino s’appuie.
* Package de la carte DevKit : chaînes d’outils, bibliothèques et projets pour le kit DevKit.
* Utilitaire ST-Link : pilotes et outils essentiels.

#### <a name="run-the-installation-script"></a>Exécuter le script d’installation

Dans l’Explorateur de fichiers Windows, localisez le fichier .zip et extrayez-le. Recherchez le fichier install.cmd, cliquez dessus avec le bouton droit et sélectionnez **Exécuter en tant qu’administrateur**.

![Explorateur de fichiers](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Pendant l’installation, vous voyez la progression de chaque outil et package.

![Progression de l’installation](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="install-drivers"></a>Installer les pilotes

L’extension VS Code pour Arduino s’appuie sur l’IDE Arduino. Si c’est la première fois que vous installez l’IDE Arduino, vous êtes amené à installer les pilotes correspondants :

![bien-démarrer-pilote](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

L’installation devrait prendre environ 10 minutes, en fonction de la vitesse de votre connexion Internet. Après l’installation, les raccourcis Visual Studio Code et IDE Arduino apparaissent sur votre bureau.

> [!NOTE] 
> Au démarrage de VS Code, il peut arriver qu’une erreur s’affiche indiquant que l’IDE Arduino ou un package de carte associé est introuvable. Fermez VS Code et redémarrez l’IDE Arduino. VS Code devrait ainsi localiser correctement le chemin de l’IDE Arduino.


### <a name="macos-preview"></a>mac OS (préversion)

Suivez ces étapes pour préparer l’environnement de développement sous macOS.

#### <a name="install-azure-cli-20"></a>Installer Azure CLI 2.0

1. Installez Azure CLI 2.0 au moyen d’une commande `curl` :

   ```bash
   curl -L https://aka.ms/InstallAzureCli | bash
   ```

2. Redémarrez votre interface de commande pour que les modifications soient prises en compte :

   ```bash
   exec -l $SHELL
   ```

Pour plus d’informations sur l’installation d’Azure CLI 2.0, consultez le [guide officiel](https://docs.microsoft.com//cli/azure/install-azure-cli).

#### <a name="install-the-arduino-ide"></a>Installer l’IDE Arduino

L’extension Arduino Visual Studio Code s’appuie sur l’IDE Arduino. Téléchargez et installez [l’IDE Arduino pour macOS](https://www.arduino.cc/en/Main/Software).

#### <a name="install-visual-studio-code"></a>Installation de Visual Studio Code

Téléchargez et installez [Visual Studio Code pour macOS](https://code.visualstudio.com/). C’est le principal outil de développement pour créer des applications IoT DevKit.

####  <a name="download-the-latest-package"></a>Télécharger le dernier package

1. Installez Node.js. Vous pouvez utiliser le gestionnaire de package macOS courant nommé [Homebrew](https://brew.sh/) ou le [programme d’installation préintégré](https://nodejs.org/en/download/) pour l’installer.

2. Téléchargez le fichier .zip qui contient les scripts de tâches nécessaires pour le développement DevKit dans VS Code.

   > [!div class="button"]
   [Télécharger](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

3. Recherchez le fichier .zip et extrayez-le. Démarrez ensuite l’application **Terminal** et exécutez les commandes suivantes :

   a. Déplacez le dossier extrait dans votre dossier d’utilisateur macOS :
      ```bash
      mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
      ```
  
   b. Installez les packages npm :
      ```
      npm install
      ```

#### <a name="install-the-vs-code-extension-for-arduino"></a>Installer l’extension VS Code pour Arduino

Vous pouvez installer les extensions de la Place de marché Microsoft Azure directement dans Visual Studio Code. Sélectionnez l’icône des extensions dans le volet gauche, recherchez **Arduino** et sélectionnez **Installer** :

![Recherche d’une extension Arduino](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-the-devkit-board-package"></a>Installer le package de carte DevKit

Ajoutez la carte DevKit à l’aide du gestionnaire de carte de Visual Studio Code.

1. Utilisez Cmd+Maj+P pour ouvrir la palette de commandes, tapez **Arduino**, puis recherchez et sélectionnez **Arduino : Gestionnaire de carte**.

2. Sélectionnez **URL supplémentaires** en bas à droite.
   ![Lien URL supplémentaires](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. Dans le fichier settings.json, ajoutez une ligne au bas du volet **USER SETTINGS** et enregistrez.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![Code ajouté au volet USER SETTINGS (paramètres utilisateur)](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. Dans le gestionnaire de carte, recherchez **az3166** et installez la dernière version.
   ![Installation d’az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

Tous les outils et packages nécessaires sont à présent installés pour macOS.


## <a name="open-the-project-folder"></a>Ouvrir le dossier de projet

### <a name="start-vs-code"></a>Démarrer VS Code

Assurez-vous que votre DevKit n’est pas connecté. Démarrez d’abord VS Code et branchez ensuite le kit DevKit sur votre ordinateur. VS Code trouve automatiquement le kit DevKit et ouvre une page de présentation :

![Page de présentation](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
> Au démarrage de VS Code, il peut arriver qu’une erreur s’affiche indiquant que l’IDE Arduino ou un package de carte associé est introuvable. Fermez VS Code et redémarrez l’IDE Arduino. VS Code devrait ainsi localiser correctement le chemin de l’IDE Arduino.


### <a name="open-the-arduino-examples-folder"></a>Ouvrir le dossier des exemples Arduino

Sous l’onglet **Exemples Arduino**, accédez à **Exemples pour MXCHIP AZ3166** > **AzureIoT** et sélectionnez **GetStarted**.

![Onglet des exemples Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

S’il vous arrive de fermer le volet par inadvertance, vous pouvez le rouvrir. Utilisez Ctrl+Maj+P (macOS : Cmd+Maj+P) pour ouvrir la palette de commandes, tapez **Arduino**, recherchez et sélectionnez **Arduino : Exemples**.

## <a name="provision-azure-services"></a>Approvisionner les services Azure

Dans la fenêtre de la solution, exécutez votre tâche via Ctrl+P (macOS : Cmd+P) en entrant `task cloud-provision`.

Dans le terminal VS Code, une ligne de commande interactive vous guide dans l’approvisionnement des services Azure nécessaires :

![Ligne de commande interactive](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Générer et charger l’ébauche de projet Arduino

### <a name="install-the-required-library"></a>Installer la bibliothèque voulue

1. Sélectionnez la touche F1 ou Ctrl+Maj+P (macOS : Cmd+Maj+P) pour ouvrir la palette de commandes, tapez **Arduino**, recherchez et sélectionnez **Arduino: Library Manager** (Arduino : Gestionnaire de bibliothèque).

2. Recherchez la bibliothèque **ArduinoJson** et sélectionnez **Installer**.

### <a name="build-and-upload-the-device-code"></a>Générer et charger le code de l’appareil

Utilisez Ctrl+P (macOS : Cmd+P) pour exécuter `task device-upload`. Le terminal vous invite à passer en mode de configuration. Pour cela, maintenez le bouton A enfoncé, appuyez sur le bouton de réinitialisation et relâchez. L’écran affiche **Configuration**. Définissez la chaîne de connexion qui est récupérée à l’étape `task cloud-provision`.

Ensuite, le terminal commence la vérification et le chargement de l’ébauche de projet Arduino :

![Vérification et téléchargement de l’ébauche de projet Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Le DevKit redémarre et commence à exécuter le code.

## <a name="test-the-project"></a>Tester le projet

Dans VS Code, sélectionnez l’icône de la prise électrique dans la barre d’état pour ouvrir Serial Monitor (Analyse en série).

L’exemple d’application s’exécute correctement si les résultats suivants s’affichent :

* Serial Monitor (Analyse en série) affiche les mêmes informations que celles de la capture d’écran suivante.
* Le témoin lumineux du kit MXChip IoT DevKit clignote.

![Sortie finale dans VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problèmes et commentaires

Si vous rencontrez des problèmes, vous pouvez consulter [Questions fréquentes (FAQ)](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Vous pouvez également nous faire part de vos remarques en laissant un commentaire sur cette page.

## <a name="next-steps"></a>Étapes suivantes

Vous avez correctement connecté un kit MXChip IoT DevKit à votre hub IoT et avez envoyé des données de capteur collectées à votre hub IoT.

Pour continuer d’apprendre à utiliser Azure IoT Hub et explorer les autres scénarios IoT, consultez les articles suivants :

- [Gérer la messagerie d’appareils cloud avec iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Enregistrer les messages IoT Hub dans le stockage de données Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Utiliser Power BI pour visualiser des données de capteur en temps réel provenant d’Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Utiliser Web Apps pour visualiser des données de capteur en temps réel depuis Azure IoT Hub](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Prévision météo utilisant les données de capteur de votre IoT Hub dans Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Gestion des appareils avec iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Surveillance à distance et notifications avec Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)

