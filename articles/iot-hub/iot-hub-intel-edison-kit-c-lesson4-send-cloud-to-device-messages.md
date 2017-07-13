---
title: "Connecter Intel Edison (C) à Azure IoT - Leçon 4 : Recevoir des messages | Microsoft Docs"
description: "Un exemple d’application s’exécute sur Edison et surveille les messages entrants à partir de votre IoT Hub. Une nouvelle tâche gulp envoie des messages à Edison à partir de votre IoT Hub pour faire clignoter la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "contrôle de la led avec arduino à partir du web, contrôle de la led avec arduino via le web"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 820d38f3-d3b8-4249-9e2b-f1b9b771e62f
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: b4d76eeb1bc0b406800c89c4952900350d8eafa8
ms.contentlocale: fr-fr
ms.lasthandoff: 01/25/2017

---
<a id="run-a-sample-application-to-receive-cloud-to-device-messages" class="xliff"></a>

# Exécution d’un exemple d’application pour recevoir des messages cloud-à-appareil
Dans cet article, vous déployez un exemple d’application sur Intel Edison. L’exemple d’application surveille les messages entrants à partir de votre IoT Hub. Vous exécutez également une tâche gulp sur votre ordinateur pour envoyer des messages à Edison à partir de votre IoT Hub. À la réception des messages, l’exemple d’application fait clignoter la LED. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre
* Connectez l’exemple d’application à votre IoT Hub.
* Déployez et exécutez l'exemple d'application.
* Envoyez des messages à partir de votre IoT Hub vers Edison pour faire clignoter la LED.

<a id="what-you-will-learn" class="xliff"></a>

## Contenu
Cet article portera sur les éléments suivants :
* Surveillance des messages entrants à partir de votre IoT Hub.
* Envoi de messages cloud-à-appareil à partir de votre IoT Hub vers Edison.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin
* Intel Edison, configuré et prêt à l’emploi. Pour savoir comment configurer Edison, consultez [Configuration de votre appareil][configure-your-device].
* Un IoT Hub créé dans le cadre de votre abonnement Azure. Pour savoir comment créer votre Azure IoT Hub, consultez [Création de votre Azure IoT Hub][create-your-azure-iot-hub].

<a id="connect-the-sample-application-to-your-iot-hub" class="xliff"></a>

## Connexion de l’exemple d’application à votre IoT Hub
1. Assurez-vous que vous êtes dans le dossier du référentiel `iot-hub-c-edison-getting-started`. Ouvrez l’exemple d’application dans Visual Studio Code en exécutant les commandes suivantes :

   ```bash
   cd Lesson4
   code .
   ```

   Le fichier du sous-dossier `app` est le fichier source clé qui contient le code permettant de surveiller les messages entrants à partir du IoT Hub. La fonction `blinkLED` fait clignoter la LED.

   ![Structure de référentiel dans l’exemple d’application][repo-structure]
2. Initialisez le fichier de configuration en exécutant les commandes suivantes :

   ```bash
   npm install
   gulp init
   ```

   Si vous avez effectué les étapes de la section [Créer une application de fonction Azure et un compte de stockage][create-an-azure-function-app-and-storage-account] sur cet ordinateur, toutes les configurations sont héritées, donc vous pouvez passer à la tâche de déploiement et d’exécution de l’exemple d’application. Si vous avez effectué les étapes de la section [Créer une application de fonction Azure et un compte de stockage][create-an-azure-function-app-and-storage-account] sur un autre ordinateur, vous devez remplacer les espaces réservés dans le fichier `config-edison.json`. Le fichier `config-edison.json` se trouve dans le sous-dossier de votre dossier racine.

   ![Contenu du fichier config-edison.json](media/iot-hub-intel-edison-lessons/lesson4/config-edison.png)

   * Remplacez **[nom d’hôte ou adresse IP de l’appareil]** par l’adresse IP d’appareil obtenue lorsque vous avez configuré votre appareil.
   * Remplacez **[chaîne de connexion d’appareil IoT]** par la chaîne de connexion d’appareil que vous obtenez en exécutant la commande `az iot device show-connection-string --hub-name {my hub name} --device-id {device id}`.
   * Remplacez **[chaîne de connexion d’IoT Hub]** par la chaîne de connexion IoT Hub que vous obtenez en exécutant la commande `az iot hub show-connection-string --name {my hub name}`.

   > [!NOTE]
   > Exécutez également **gulp install-tools**, si vous ne l’avez pas fait dans la leçon 1.

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

## Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application sur Edison en exécutant les commandes suivantes :

```bash
gulp deploy && gulp run
```

La commande gulp déploie l’exemple d’application sur Edison. Ensuite, elle exécute l’application sur Edison et une tâche distincte sur votre ordinateur hôte afin d’envoyer 20 messages de clignotement à Edison à partir de votre IoT Hub.

Une fois l’exemple d’application exécuté, il commence à écouter les messages à partir de votre IoT Hub. Pendant ce temps, la tâche gulp envoie plusieurs messages de « clignotement » à partir de votre IoT Hub vers Edison. Pour chaque message de clignotement reçu par Edison, l’exemple d’application demande à la fonction `blinkLED` de faire clignoter la LED.

La LED doit clignoter toutes les deux secondes tandis que la tâche gulp envoie 20 messages de votre IoT Hub vers Edison. Le dernier message est un message « stop » qui interrompt l’exécution de l’application.

![Exemple d’application avec la commande gulp et les messages de clignotement][gulp-command-and-blink-messages]

<a id="summary" class="xliff"></a>

## Résumé
Vous avez correctement envoyé des messages à partir de votre IoT Hub vers Edison pour faire clignoter la LED. La tâche suivante est facultative : modifier le comportement activé/désactivé de la LED.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Modification du comportement activé/désactivé de la LED][change-the-on-and-off-behavior-of-the-led]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[create-your-azure-iot-hub]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson4/repo_structure_c.png
[create-an-azure-function-app-and-storage-account]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
[gulp-command-and-blink-messages]: media/iot-hub-intel-edison-lessons/lesson4/gulp_blink_c.png
[change-the-on-and-off-behavior-of-the-led]: iot-hub-intel-edison-kit-c-lesson4-change-led-behavior.md
