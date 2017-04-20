---
title: "Connecter Raspberry Pi (C) à Azure IoT - Résolution des problèmes | Microsoft Docs"
description: "Page Résolution des problèmes pour l’expérience Node.js Raspberry Pi"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "problèmes iot, problèmes liés à l’internet des objets"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: 22cf50dc-8206-42a2-a1fc-f75fa85135fa
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 2c5e2955584b165ca0ca95ce424a2050fb3f6ccd
ms.lasthandoff: 01/24/2017


---
# <a name="troubleshooting"></a>Résolution des problèmes
## <a name="hardware-issues"></a>Problèmes matériels
### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>L’application s’exécute correctement, mais la LED ne clignote pas
Ce problème est toujours lié à la connectivité du circuit matériel. Pour identifier les problèmes, procédez comme suit :

1. Assurez-vous d’avoir choisi le port **GPIO** correct sur votre carte. Les deux ports utilisés doivent être **GPIO GND (broche 6)** et **GPIO 04 (broche 7)**.
2. Vérifiez que la polarité de votre LED est correcte. Le segment le plus long doit indiquer la broche de l’anode, **positif**.
3. Utilisez la **broche 3.3V** et la **broche GND** sur Raspberry Pi 3. Utilisez Pi comme alimentation en courant continu. Vérifiez que la LED fonctionne correctement.

![Spécification de la LED](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Autres problèmes matériels
Pour plus d’informations sur la résolution de problèmes courants sur Raspberry Pi 3, consultez la [page officielle de résolution des problèmes](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Problèmes de package Node.js
### <a name="no-response-during-gulp-tasks"></a>Aucune réponse lors des tâches gulp
Si vous rencontrez des problèmes d’exécution des tâches gulp, vous pouvez ajouter l’option `--verbose` pour le débogage. Essayez d’arrêter les tâches gulp en cours en appuyant sur Ctrl + C, puis exécutez la commande suivante dans la fenêtre de la console pour afficher les messages de débogage. Des messages d’erreur détaillés peuvent s’afficher dans la sortie de la console.

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problèmes de détection d’appareil
Pour obtenir de l’aide sur la résolution de problèmes courants liés à la commande `devdisco`, consultez la rubrique [Lisez-moi](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="npm-issues"></a>problèmes liés à npm
Essayez de mettre à jour votre package npm avec la commande suivante :

```bash
npm install -g npm
```

Si le problème persiste, entrez vos commentaires à la fin de cet article ou créez un problème GitHub dans notre [exemple de référentiel](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started).

## <a name="remote-debugging"></a>Débogage à distance
### <a name="run-the-sample-application-in-debug-mode"></a>Exécuter l’application en mode débogage
```bash
gulp run --debug
```

Lorsque le moteur de débogage est prêt, ```Debugger listening on port 5858``` doit apparaître dans la sortie de la console.

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>Configuration de Visual Studio Code pour la connexion à l’appareil distant
1. Ouvrez le panneau **Déboguer** dans la partie gauche.
2. Cliquez sur le bouton vert **Démarrer le débogage** (F5). Visual Studio Code ouvre un fichier launch.json.
3. Mettez à jour le fichier launch.json avec le contenu suivant. Remplacez `[device hostname or IP address]` par l’adresse IP ou le nom d’hôte de l’appareil.

> [!NOTE]
> Pour plus d’informations sur le débogage de Visual Studio, consultez [Débogage dans Visual Studio Code](https://code.visualstudio.com/Docs/editor/debugging#_launchjson-attributes).


```json
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
            "remoteRoot": null
        }
    ]
}
```

![Configuration du débogage à distance](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Attacher à l’application distante
Cliquez sur le bouton vert **Démarrer le débogage** (F5) pour commencer à déboguer.

Pour en savoir plus sur le débogueur, lisez [JavaScript dans VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) (JavaScript dans Visual Studio Code).

![Débogage interactif à distance](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Problèmes d’interface de ligne de commande Azure
L’interface de ligne de commande Azure est une version d’évaluation. Pour rechercher des solutions, vous pouvez utiliser le [Guide d’installation de la version d’évaluation](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md).

Si vous rencontrez des bogues avec l’outil, documentez un [problème](https://github.com/Azure/azure-cli/issues) dans la section **Issues** du dépôt GitHub.

Pour obtenir de l’aide sur la résolution de problèmes courants, consultez le fichier [Lisez-moi](https://github.com/Azure/azure-cli/blob/master/README.rst).

## <a name="python-installation-issues"></a>Problèmes d’installation de Python
### <a name="legacy-installation-issues-macos"></a>Problèmes d’installation héritée (macOS)
Lors de l’installation de pip, une erreur d’autorisation est levée s’il existe des packages antérieurs qui sont installés avec des autorisations **su**. Cette situation se produit parce qu’une installation précédente de Python utilisant brew (macOS) n’est pas complètement désinstallée. Certains packages pip d’une installation précédente ont été créés par root, ce qui génère l’erreur d’autorisation. La solution consiste à supprimer les packages installés par root. Pour effectuer cette tâche, procédez comme suit :

1. Accédez à : /usr/local/lib/python2.7/site-packages
2. Affichez la liste des packages créés par root : `ls -l | grep root`
3. Désinstallez les packages identifiés à l’étape 2 : `sudo rm -rf {package name}`
4. Réinstallez Python.

## <a name="azure-iot-hub-issues"></a>Problèmes liés à Azure IoT Hub
Si vous avez correctement configuré votre Azure IoT Hub avec l’interface de ligne de commande Azure, et avez besoin d’un outil pour gérer les appareils se connectant à votre IoT Hub, essayez les outils suivants.

### <a name="device-explorer"></a>Explorateur d’appareils
L’[Explorateur d’appareils](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) s’exécute sur votre ordinateur Windows local et se connecte à votre IoT Hub dans Azure. Il communique avec les [points de terminaison IoT Hub](iot-hub-devguide.md) suivants :


* *Device identity management* (Gestion d’identité d’appareil) pour configurer et gérer les appareils inscrits auprès de votre IoT Hub.
* *Receive device-to-cloud* (Réception appareil-à-cloud) pour pouvoir surveiller les messages envoyés par votre appareil à votre IoT Hub.
* *Send cloud-to-device* (Envoi cloud-à-appareil) pour pouvoir envoyer des messages à vos appareils à partir de votre IoT Hub.

Configurez la chaîne de connexion de votre IoT Hub dans cet outil pour utiliser toutes ses fonctionnalités.

### <a name="iothub-explorer"></a>iothub-explorer
[iothub-explorer](https://github.com/Azure/iothub-explorer) est un exemple d’outil d’interface de ligne de commande multiplateforme destiné à la gestion des appareils. Cet outil permet de gérer les appareils dans le registre des identités, de surveiller les messages appareil-à-cloud et d’envoyer des messages cloud-à-appareil.

Pour installer la dernière version (préliminaire) de l’outil iothub-explorer, dans votre environnement de ligne de commande, exécutez la commande suivante :

```bash
npm install -g iothub-explorer@latest
```

Pour obtenir une aide supplémentaire sur toutes les commandes d’iothub-explorer et leurs paramètres, vous pouvez utiliser la commande suivante :

```bash
iothub-explorer help
```

### <a name="azure-portal"></a>Portail Azure
Une expérience complète de l’interface de ligne de commande vous permet de créer et de gérer toutes vos ressources Azure. Vous pouvez également être amené à utiliser le [portail Azure](../azure-portal-overview.md) pour configurer, gérer et déboguer vos ressources Azure.

## <a name="azure-storage-issues"></a>Problèmes liés au Stockage Azure
[L’Explorateur de stockage Microsoft Azure (en version préliminaire)](http://storageexplorer.com) est une application autonome de Microsoft qui vous permet d’utiliser des données du Stockage Azure sur Windows, macOS et Linux. Cet outil vous permet de vous connecter à votre table et d’en consulter les données. Vous pouvez l’utiliser cet outil pour résoudre des problèmes liés au Stockage Azure.


