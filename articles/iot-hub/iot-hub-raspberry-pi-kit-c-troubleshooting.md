---
title: "Résolution des problèmes | Microsoft Docs"
description: "Page Dépannage pour l’expérience Node.js Raspberry Pi"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "problèmes iot, problèmes liés à l’internet des objets"
ms.assetid: 3653c79b-8a0c-41d4-b0bf-f6b4edb4d233
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: e3e4ad430d8941a09543ce2dc97f8e449a39bced
ms.openlocfilehash: 755961b8a0b141a716530f75e21b65c00fa8d2d8


---
# <a name="troubleshooting"></a>Résolution des problèmes
## <a name="hardware-issues"></a>Problèmes matériels
### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>L’application s’exécute correctement, mais la LED ne clignote pas
Ce problème est toujours lié à la connectivité du circuit matériel. Pour identifier les problèmes, procédez comme suit.

1. Assurez-vous d’avoir choisi le port **GPIO** correct sur votre carte. Les deux ports utilisés doivent être **GPIO GND (broche 6)** et **GPIO 04 (broche 7)**.
2. Vérifiez que la polarité de votre LED est correcte. Le segment le plus long doit indiquer la broche de l’anode, **positif**.
3. Utilisez la **broche 3.3V** et la **broche GND** sur Raspberry Pi 3. Utilisez Pi comme alimentation en courant continu. Vérifiez que la LED fonctionne correctement.

![Spécification de la LED](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Autres problèmes matériels
Pour plus d’informations sur la résolution de problèmes courants sur Raspberry Pi 3, consultez la [page officielle de résolution des problèmes](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Problèmes de package Node.js
### <a name="no-response-during-gulp-tasks"></a>Aucune réponse lors des tâches gulp
Si vous rencontrez des problèmes d’exécution des tâches gulp, vous pouvez ajouter l’option `--verbose` pour le débogage. Essayez d’arrêter les tâches gulp en cours en appuyant sur `Ctrl + C`, puis exécutez la commande suivante dans la fenêtre de la console pour afficher les messages de débogage. Des messages d’erreur détaillés peuvent s’afficher dans la sortie de la console. 

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problèmes de détection d’appareil
Pour obtenir de l’aide sur la résolution de problèmes courants liés à la commande `devdisco`, consultez la rubrique [Lisez-moi](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="npm-issues"></a>Problèmes liés à NPM
Essayez de mettre à jour votre package NPM avec la commande suivante :

```bash
npm install -g npm
```

Si le problème persiste, entrez vos commentaires à la fin de cet article ou créez un problème GitHub dans notre [exemple de référentiel](https://github.com/Azure-Samples/iot-hub-c-raspberrypi-getting-started)

## <a name="remote-debugging"></a>Débogage à distance

La prise en charge du débogage à distance sera disponible prochainement dans l’extension C/C++ de Visual Studio Code.

En attendant, vous pouvez utiliser GDB via votre terminal SSH favori :

```bash
cd c-pi-lesson-x
sudo gdb app
```

## <a name="azure-cli-issues"></a>Problèmes liés à l'interface de ligne de commande Azure
L’interface de ligne de commande Azure est une version d’évaluation. Pour rechercher des solutions, voir le [Guide d’installation de la version d’évaluation](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md). Essayez de mettre à niveau Azure-cli à la version la plus récente lorsque les commandes ne fonctionnent pas comme prévu.

Si vous rencontrez des bogues avec l’outil, documentez un [problème](https://github.com/Azure/azure-cli/issues) dans la section **Issues** du dépôt GitHub.

Pour obtenir de l’aide sur la résolution de problèmes courants, voir la rubrique [readme](https://github.com/Azure/azure-cli/blob/master/README.rst).

Si l’erreur « Impossible de trouver une version conforme à la configuration requise » s’affiche, exécutez la commande suivante pour mettre à niveau pip vers la dernière version.

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Problèmes d’installation de Python
### <a name="legacy-installation-issues-macos"></a>Problèmes d’installation héritée (macOS)
Lors de l’installation de **pip**, une erreur d’autorisation est levée s’il existe des packages antérieurs qui sont installés avec des autorisations **su**. Cette situation se produit parce qu’une installation précédente de Python utilisant brew (macOS) n’est pas complètement désinstallée. Certains packages **pip** d’une installation précédente ont été créés par root, ce qui génère l’erreur d’autorisation. La solution consiste à supprimer les packages installés par root. Pour effectuer cette tâche, procédez comme suit :

1. Accédez à : /usr/local/lib/python2.7/site-packages
2. Affichez la liste des packages créés par root : `ls -l | grep root`.
3. Désinstallez les packages identifiés à l’étape 2 : `sudo rm -rf {package name}`
4. Réinstallez Python.

## <a name="azure-iot-hub-issues"></a>Problèmes liés à Azure IoT Hub
Si vous avez correctement configuré votre Azure IoT Hub avec `azure-cli`, et avez besoin d’un outil pour gérer les appareils se connectant à votre IoT Hub, essayez les outils suivants :

### <a name="device-explorer"></a>Explorateur d’appareils
L’[Explorateur d’appareils](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) s’exécute sur votre ordinateur Windows local et se connecte à votre IoT Hub dans Azure. Il communique avec les [points de terminaison IoT Hub](iot-hub-devguide.md) suivants :

* *Device identity management* (Gestion d’identité d’appareil) pour configurer et gérer les appareils inscrits auprès de votre IoT Hub.
* *Receive device-to-cloud* (Réception appareil-à-cloud) pour pouvoir surveiller les messages envoyés par votre appareil à votre IoT Hub.
* *Send cloud-to-device* (Envoi cloud-à-appareil) pour pouvoir envoyer des messages à vos appareils à partir de votre IoT Hub.

Configurez votre `IoT hub connection string` dans cet outil pour utiliser toutes ses fonctionnalités.

### <a name="iot-hub-explorer"></a>IoT Hub Explorer
[IoT Hub Explorer](https://github.com/Azure/iothub-explorer) est un exemple d’outil d’interface de ligne de commande multiplateforme destiné à la gestion d’appareils clients. Cet outil permet de gérer les appareils dans le registre des identités, de surveiller les messages appareil-à-cloud et d’envoyer des commandes cloud-à-appareil.

Pour installer la dernière version (préliminaire) de l’outil iothub-explorer, dans votre environnement de ligne de commande, exécutez la commande suivante :

```
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



<!--HONumber=Jan17_HO2-->


