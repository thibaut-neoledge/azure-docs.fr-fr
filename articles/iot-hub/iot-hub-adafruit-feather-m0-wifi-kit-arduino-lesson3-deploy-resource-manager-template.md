---
title: "Connecter Arduino (C) à Azure IoT - Leçon 3 : Déploiement de modèle | Microsoft Docs"
description: "L’application de fonction Azure écoute les événements d’Azure IoT Hub, traite les messages entrants et les écrit dans le stockage Table Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "stockage de données dans le cloud, les données stockées dans le cloud, service cloud iot"
ms.assetid: 9c8f4cd1-9511-4601-ad7e-51761a986753
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: cf666d17406a49036082a6a4dd15bad89c04aa8f


---

# <a name="create-an-azure-function-app-and-azure-storage-account"></a>Créer une application de fonction Azure et un compte de Stockage Azure
[Azure Functions](../../articles/azure-functions/functions-overview.md) est une solution conçue pour exécuter facilement des petits morceaux de code (« *functions* ») dans le cloud. Une application de fonction Azure héberge l’exécution de vos fonctions dans Azure.

## <a name="what-will-you-do"></a>Ce que vous allez faire
Utilisez un modèle Azure Resource Manager pour créer une application de fonction Azure et un compte de Stockage Azure. L’application de fonction Azure écoute les événements d’Azure IoT Hub, traite les messages entrants et les écrit dans le stockage Table Azure.

Si vous rencontrez des problèmes, recherchez une solution sur la [page Résolution des problèmes pour la carte Adafruit Feather M0 WiFi Arduino](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md).

## <a name="what-will-you-learn"></a>Ce que vous allez apprendre
Cet article portera sur les éléments suivants :
* Commet utiliser [Azure Resource Manager](../../articles/azure-resource-manager/resource-group-overview.md) pour déployer des ressources Azure.
* Comment utiliser une application de fonction Azure pour traiter les messages de l’IoT Hub et les écrire dans une table du stockage Table Azure.

## <a name="what-do-you-need"></a>Ce dont vous avez besoin
Vous devez avoir accompli avec succès les étapes :
- [Bien démarrer avec la carte Arduino][get-started]
- [Créer votre Azure IoT Hub][create-iot-hub]

## <a name="open-the-sample-app"></a>Ouvrir l’exemple d’application
Ouvrez l’exemple de projet dans Visual Studio Code en exécutant les commandes suivantes :

```bash
cd Lesson3
code .
```

![Structure du référentiel][repo-structure]

* Le fichier `app.ino` se trouvant dans le sous-dossier `app` est le fichier source clé. Ce fichier source contient le code pour envoyer un message 20 fois à votre IoT hub et faire clignoter la LED pour chaque message envoyé.
* Le fichier `config.json` contient les paramètres de configuration requis.
* Le fichier `arm-template.json` est le modèle Azure Resource Manager qui contient une application de fonction Azure et un compte de Stockage Azure.
* Le fichier `arm-template-param.json` est le fichier de configuration utilisé par le modèle Azure Resource Manager.
* Le sous-dossier `ReceiveDeviceMessages` contient le code Node.js pour la fonction Azure.

## <a name="configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>Configurer des modèles Azure Resource Manager et créer des ressources dans Azure
Mettez à jour le fichier `arm-template-param.json` dans Visual Studio Code.

![Paramètres de modèle Azure Resource Manager][arm-template-params]

* Remplacez **[nom de votre IoT Hub]** par **{nom de mon hub}** que vous avez spécifié lorsque vous [avez créé votre IoT Hub et inscrit votre carte Arduino][created-iot-hub-and-registered-arduino-board].
* Remplacez la **[chaîne de préfixe pour les nouvelles ressources]** par un préfixe de votre choix quelconque. Le préfixe vous assure que le nom de ressource est globalement unique pour éviter tout conflit. N’utilisez pas de tiret ni de chiffre initial dans le préfixe.

Une fois que vous avez mis à jour le fichier `arm-template-param.json`, déployez les ressources dans Azure en exécutant la commande suivante :

```bash
az group deployment create --template-file arm-template.json --parameters @arm-template-param.json -g iot-sample
```

La création de ces ressources prend environ cinq minutes. Pendant la création de ressources, vous pouvez passer à l’article suivant.

## <a name="summary"></a>Résumé
Vous avez créé votre application de fonction Azure pour traiter les messages de l’IoT Hub et un compte de Stockage Azure pour stocker ces messages. Vous pouvez désormais déployer et exécuter l’exemple pour envoyer des messages appareil-à-cloud sur votre carte Arduino.

## <a name="next-steps"></a>Étapes suivantes
[Exécuter un exemple d’application pour envoyer des messages appareil-à-cloud sur votre carte Arduino][send-device-to-cloud-messages]

<!-- Images and links -->

[get-started]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[create-iot-hub]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/repo_structure_c.png
[arm-template-params]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson3/arm_para_arduino.png
[created-iot-hub-and-registered-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md
[send-device-to-cloud-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-run-azure-blink.md


<!--HONumber=Jan17_HO4-->


