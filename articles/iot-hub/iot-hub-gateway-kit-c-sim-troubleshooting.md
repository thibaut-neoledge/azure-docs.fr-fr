---
title: "Appareil simulé et passerelle Azure IoT - Résolution de problèmes | Microsoft Docs"
description: "Page de résolution des problèmes pour la passerelle Intel NUC"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "problèmes iot, problèmes liés à l’internet des objets"
ROBOTS: NOINDEX
ms.assetid: 3ee8f4b0-5799-40a3-8cf0-8d5aa44dbc2b
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a70de978d4636a31644702c6f3a8ca0d0b80f5c2
ms.lasthandoff: 01/25/2017


---
# <a name="troubleshooting"></a>Résolution des problèmes

## <a name="hardware-issues"></a>Problèmes matériels

### <a name="ti-sensortag-cannot-be-connected"></a>TI SensorTag ne peut pas être connecté

Pour résoudre les problèmes de connectivité de SensorTag, utilisez [l’application SensorTag](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide).

### <a name="have-an-issue-with-intel-nuc"></a>Problème avec Intel NUC

Pour résoudre les problèmes de démarrage, consultez [Résolution des problèmes Absence de démarrage sur Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html).

Pour résoudre les problèmes du système d’exploitation, consultez [Résolution des problèmes Système d’exploitation sur Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html).

Pour résoudre d’autres problèmes, consultez [Codes Blink et codes Beep pour Intel NUC](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html).

## <a name="nodejs-package-issues"></a>Problèmes de package Node.js

### <a name="no-response-during-gulp-tasks"></a>Aucune réponse lors des tâches gulp

Si vous rencontrez des problèmes d’exécution des tâches gulp, vous pouvez ajouter l’option `--verbose` pour le débogage. Essayez d’arrêter les tâches gulp en cours en appuyant sur `Ctrl + C`, puis exécutez la commande suivante dans la fenêtre de la console pour afficher les messages de débogage. Des messages d’erreur détaillés peuvent s’afficher dans la sortie de la console.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problèmes de détection d’appareil

Pour obtenir de l’aide sur la résolution de problèmes courants liés à la commande `discover-sensortag`, consultez la [page wiki](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl).

### <a name="npm-issues"></a>problèmes liés à npm

Essayez de mettre à jour votre package npm en exécutant la commande suivante :

```bash
npm install -g npm
```

Si le problème persiste, entrez vos commentaires à la fin de cet article ou créez un problème GitHub dans notre [exemple de référentiel](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started).

## <a name="remote-debugging"></a>Débogage à distance
> Les instructions ci-dessous s’appliquent au débogage des scripts node.js utilisés dans ce didacticiel.
### <a name="run-the-sample-application-in-debug-mode"></a>Exécuter l’application en mode débogage

Exécutez l’exemple d’application en mode débogage en exécutant la commande suivante :

```bash
gulp run --debug
```

Lorsque le moteur de débogage est prêt, `Debugger listening on port 5858` doit apparaître dans la sortie de la console.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Configuration de Visual Studio Code pour la connexion à l’appareil distant

1. Ouvrez le panneau **Déboguer** dans la partie gauche.
2. Cliquez sur le bouton vert **Démarrer le débogage** (F5). Visual Studio Code ouvre un fichier `launch.json`.
3. Mettez le fichier `launch.json` à jour avec le contenu suivant. Remplacez `[device hostname or IP address]` par l’adresse IP ou le nom d’hôte de l’appareil.

   ``` json
   {
     "version": "0.2.0",
     "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![Configuration du débogage à distance](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Attacher à l’application distante

Cliquez sur le bouton vert **Démarrer le débogage** (F5) pour commencer à déboguer.

Pour en savoir plus sur le débogueur, lisez [JavaScript dans VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) (JavaScript dans Visual Studio Code).

![Exemple de débogage BLE](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Problèmes d’interface de ligne de commande Azure

L’interface de ligne de commande Azure est une version d’évaluation. Pour rechercher des solutions, vous pouvez utiliser le [Guide d’installation de la version d’évaluation](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

Si vous rencontrez des bogues avec l’outil, documentez un [problème](https://github.com/Azure/azure-cli/issues) dans la section **Issues** du dépôt GitHub.

Pour obtenir de l’aide sur la résolution de problèmes courants, consultez le fichier [Lisez-moi](https://github.com/Azure/azure-cli/blob/master/README.rst).

Si l’erreur « Impossible de trouver une version conforme à la configuration requise » s’affiche, exécutez la commande suivante pour mettre à niveau pip vers la dernière version.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Problèmes d’installation de Python

### <a name="legacy-installation-issues-macos"></a>Problèmes d’installation héritée (macOS)

Lors de l’installation de pip, une erreur d’autorisation est levée s’il existe des packages antérieurs qui sont installés avec des autorisations **su**. Cette situation se produit parce qu’une installation précédente de Python utilisant brew (macOS) n’est pas complètement désinstallée. Certains packages pip d’une installation précédente ont été créés par root, ce qui génère l’erreur d’autorisation. La solution consiste à supprimer les packages installés par root. Pour effectuer cette tâche, procédez comme suit :

1. Accédez à `/usr/local/lib/python2.7/site-packages`
2. Affichez la liste des packages créés par root : `ls -l | grep root`
3. Désinstallez les packages identifiés à l’étape 2 : `sudo rm -rf {package name}`
4. Réinstallez Python.

## <a name="azure-iot-hub-issues"></a>Problèmes liés à Azure IoT Hub

Si vous avez correctement configuré votre Azure IoT Hub avec l’interface de ligne de commande Azure, et avez besoin d’un outil pour gérer les appareils se connectant à votre IoT Hub, essayez les outils suivants.

### <a name="device-explorer"></a>Explorateur d’appareils

L’[Explorateur d’appareils](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) s’exécute sur votre ordinateur Windows local et se connecte à votre IoT Hub dans Azure. Il communique avec les [points de terminaison IoT Hub](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/) suivants :

- Device identity management (Gestion d’identité d’appareil) pour configurer et gérer les appareils inscrits auprès de votre IoT Hub.
- Receive device-to-cloud (Réception appareil-à-cloud) pour pouvoir surveiller les messages envoyés par votre appareil à votre IoT Hub.
- Send cloud-to-device (Envoi cloud-à-appareil) pour pouvoir envoyer des messages à vos appareils à partir de votre IoT Hub.

Configurez la chaîne de connexion de votre IoT Hub dans cet outil pour utiliser toutes ses fonctionnalités.

### <a name="iothub-explorer"></a>iothub-explorer

[iothub-explorer](https://github.com/Azure/iothub-explorer) est un exemple d’outil d’interface de ligne de commande multiplateforme destiné à la gestion d’appareils clients. Cet outil permet de gérer les appareils dans le registre des identités, de surveiller les messages appareil-à-cloud et d’envoyer des commandes cloud-à-appareil.

Pour installer la dernière version (préliminaire) de l’outil iothub-explorer, exécutez la commande suivante :

```bash
npm install -g iothub-explorer@latest
```

Pour obtenir une aide supplémentaire sur toutes les commandes d’iothub-explorer et leurs paramètres, exécutez la commande suivante :

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>Le portail Azure

Une expérience complète de l’interface de ligne de commande vous permet de créer et de gérer toutes vos ressources Azure. Vous pouvez également être amené à utiliser le [portail Azure](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/) pour configurer, gérer et déboguer vos ressources Azure.

## <a name="azure-storage-issues"></a>Problèmes liés au Stockage Azure

[L’Explorateur de stockage Microsoft Azure (en version préliminaire)](http://storageexplorer.com/) est une application autonome de Microsoft qui vous permet d’utiliser des données du Stockage Azure sur Windows, macOS et Linux. Cet outil vous permet de vous connecter à votre table et d’en consulter les données. Vous pouvez l’utiliser cet outil pour résoudre des problèmes liés au Stockage Azure.

