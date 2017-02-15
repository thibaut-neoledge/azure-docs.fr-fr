---
title: "Exécution d’un exemple d’application pour envoyer des messages appareil-à-cloud à Azure IoT Hub | Microsoft Docs"
description: "Déployez et exécutez sur Intel Edison un exemple d’application qui envoie des messages à votre IoT Hub et fait clignoter la LED."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "service cloud iot, envoi de données au cloud arduino"
ms.assetid: 1b3b1074-f4d4-42ac-b32c-55f18b304b44
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: cb18052f74528e245264bb2f400b625fe550ed85
ms.openlocfilehash: b158dc91186496d9b8ebd0d4ce0478c7ae45935a


---
# <a name="run-a-sample-application-to-send-device-to-cloud-messages"></a>Exécution d’un exemple d’application pour envoyer des messages appareil-à-cloud
## <a name="what-you-will-do"></a>Procédure à suivre
Cet article vous explique comment déployer et exécuter sur Intel Edison un exemple d’application qui envoie des messages à votre IoT Hub. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

## <a name="what-you-will-learn"></a>Contenu
Vous apprendrez à utiliser l’outil gulp pour déployer et exécuter l’exemple d’application C sur Edison.

## <a name="what-you-need"></a>Ce dont vous avez besoin
* Avant de commencer cette tâche, vous devez avoir correctement suivi la section [Création d’une application de fonction Azure et d’un compte de stockage pour traiter et stocker des messages du IoT Hub][process-and-store-iot-hub-messages].

## <a name="get-your-iot-hub-and-device-connection-strings"></a>Obtenir vos chaînes de connexion d’IoT Hub et d’appareil
La chaîne de connexion de l’appareil permet de connecter Edison à votre IoT Hub. La chaîne de connexion de l’IoT Hub permet de connecter votre IoT Hub à l’identité d’appareil représentant Edison dans l’IoT Hub.

* Répertorier tous les IoT Hubs de votre groupe de ressources en exécutant la commande suivante de l’interface de ligne de commande Azure :

```bash
az iot hub list -g iot-sample --query [].name
```

Si vous n’avez pas modifié la valeur, utilisez `iot-sample` en tant que valeur de `{resource group name}`.

* Obtenez la chaîne de connexion de l’IoT Hub en exécutant la commande de l’interface de ligne de commande Azure suivante :

```bash
az iot hub show-connection-string --name {my hub name}
```

`{my hub name}` est le nom que vous avez spécifié lorsque vous avez créé votre IoT Hub et inscrit Edison.

* Obtenez la chaîne de connexion de l’appareil en exécutant la commande suivante :

```bash
az iot device show-connection-string --hub-name {my hub name} --device-id myinteledison
```

Si vous n’avez pas modifié la valeur, utilisez `myinteledison` en tant que valeur de `{device id}`.

## <a name="configure-the-device-connection"></a>Configuration de la connexion de l’appareil
1. Initialisez le fichier de configuration en exécutant les commandes suivantes :

   ```bash
   npm install
   gulp init
   ```

2. Ouvrez le fichier de configuration de l’appareil `config-edison.json` dans Visual Studio Code en exécutant la commande suivante :

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

   ![config.json](media/iot-hub-intel-edison-lessons/lesson3/config.png)
3. Dans le fichier `config-edison.json`, effectuez les remplacements suivants :

   * Remplacez **[nom d’hôte ou adresse IP de l’appareil]** par l’adresse IP d’appareil obtenue lorsque vous avez configuré votre appareil.
   * Remplacez **[chaîne de connexion de l’appareil IoT]** par la `device connection string` obtenue.
   * Remplacez **[chaîne de connexion d’IoT Hub]** par la `iot hub connection string` obtenue.

   > [!NOTE]
   > Vous n’avez pas besoin de `azure_storage_connection_string` dans cet article. Gardez-le tel quel.

## <a name="deploy-and-run-the-sample-application"></a>Déploiement et exécution de l’exemple d’application
Déployez et exécutez l’exemple d’application sur Edison en exécutant la commande suivante :

```bash
gulp deploy && gulp run
```

## <a name="verify-that-the-sample-application-works"></a>Vérification du bon fonctionnement de l’exemple d’application
Vous devez voir la LED connectée à Edison clignoter toutes les deux secondes. Chaque fois que la LED clignote, l’exemple d’application envoie un message à votre IoT Hub et vérifie que le message a été correctement envoyé. De plus, chaque message reçu par l’IoT Hub apparaît dans la fenêtre de console. L’exemple d’application se termine automatiquement après l’envoi de 20 messages.

![Exemple d’application avec des messages envoyés et reçus][sample-application-with-sent-and-received-messages]

## <a name="summary"></a>Résumé
Vous avez déployé et exécuté le nouvel exemple d’application de clignotement sur Edison pour l’envoi de messages appareil-à-cloud à votre IoT Hub. Vous surveillez désormais les messages à mesure qu’ils sont écrits dans le compte de stockage.

## <a name="next-steps"></a>Étapes suivantes
[Lecture des messages conservés dans le stockage Azure][read-messages-persisted-in-azure-storage]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-node-lesson3-deploy-resource-manager-template.md
[sample-application-with-sent-and-received-messages]: media/iot-hub-intel-edison-lessons/lesson3/gulp_run.png
[read-messages-persisted-in-azure-storage]: iot-hub-intel-edison-kit-node-lesson3-read-table-storage.md


<!--HONumber=Dec16_HO2-->


