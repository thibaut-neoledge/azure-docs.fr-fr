---
title: "Visualisation en temps réel des données de capteur depuis Azure IoT Hub : Web Apps | Microsoft Docs"
description: "Utiliser Azure Web Apps pour afficher des données sur les températures et l’humidité collectées par le capteur et envoyées à votre instance Azure IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "visualisation de données en temps réel, visualisation de données en direct, visualisation de données de capteurs"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: f3bb01da7764e467963a47d3d5485679411c9167
ms.contentlocale: fr-fr
ms.lasthandoff: 04/25/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-azure-web-apps"></a>Visualiser des données de capteur en temps réel à partir d’Azure IoT Hub en utilisant Azure Web Apps

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenu

Dans cette leçon, vous allez apprendre à visualiser les données de capteur en temps réel que votre IoT Hub Azure reçoit en exécutant une application web qui est hébergée sur une application web Azure. Si vous souhaitez essayer de visualiser les données dans votre instance IoT Hub avec Power BI, consultez la rubrique [Utiliser Power BI pour visualiser les données de capteur en temps réel à partir d’Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Procédure

- Créez une application web Azure dans le portail Azure.
- Préparez votre instance IoT Hub pour l’accès aux données via l’ajout d’un groupe de consommateurs.
- Configurez l’application web pour lire des données de capteur à partir de votre IoT Hub.
- Chargez une application web qui sera hébergée par l’application web.
- Ouvrez l’application web pour afficher des données de température et d’humidité en temps réel à partir de votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Le didacticiel [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) terminé, qui répond aux exigences suivantes :
  - Un abonnement Azure actif.
  - Une instance Azure IoT Hub associée à votre abonnement.
  - Une application cliente qui envoie des messages à votre instance Azure IoT Hub.
- Git. ([Télécharger Git](https://www.git-scm.com/downloads)).

## <a name="create-an-azure-web-app"></a>Créer une application web Azure

1. Dans le [portail Azure](https://ms.portal.azure.com/), cliquez sur **Nouveau** > **Web + Mobile** > **Application web**.
1. Saisissez un nom de tâche unique, vérifiez l’abonnement, spécifiez un groupe de ressources et un emplacement, sélectionnez **Épingler au tableau de bord**, puis cliquez sur **Créer**.

   Nous vous recommandons de sélectionner le même emplacement que celui où se trouve votre groupe de ressources. Cela est utile pour réduire la vitesse de traitement ainsi que les coûts relatifs au transfert de données.

   ![Créer une application web Azure](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Configurer l’application web pour lire des données à partir de votre IoT Hub

1. Ouvrez l’application web que vous venez de configurer.
1. Cliquez sur **paramètres de l’application**, puis ajoutez les paires clé/valeur suivantes sous **paramètres de l’application** :

   | Clé                                   | Valeur                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Obtenu à partir de iothub-explorer                                |
   | Azure.IoT.IoTHub.DeviceId             | Obtenu à partir de iothub-explorer                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | Le nom du groupe de consommateurs que vous ajoutez à votre IoT hub  |

   ![Ajouter des paramètres à l’application web Azure avec des paires clé/valeur](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Chargez une application web qui sera hébergée par l’application web

Nous avons mis à disposition une application web sur GitHub qui affiche des données de capteur en temps réel à partir de votre IoT Hub. Il vous suffit de configurer l’application web pour utiliser un référentiel Git, télécharger l’application web à partir de GitHub et la charger dans Azure pour héberger l’application web.

1. Dans l’application web, cliquez sur **Options de déploiement** > **choisir la source** > **Référentiel Git local**.

   ![Configurer votre déploiement d’application web Azure pour utiliser le référentiel Git local](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

1. Cliquez sur **Configurer une connexion**, créez un nom d’utilisateur et un mot de passe qui permettra de se connecter au référentiel Git dans Azure, puis cliquez sur **OK**.

   ![Définissez le nom d’utilisateur et le mot de passe pour le référentiel Git dans Azure pour votre application web](media/iot-hub-live-data-visualization-in-web-apps/6_web-app-set-user-password-git-repo-azure.png)

1. Cliquez sur **OK** pour terminer la configuration.
1. Cliquez sur **Vue d’ensemble** et prenez note de la valeur de **URL de clone Git**.

   ![Obtenez l’URL de clonage Git de votre application web Azure](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

1. Ouvrez une commande ou une fenêtre de terminal sur votre ordinateur local.
1. Télécharger l’application web à partir de GitHub et chargez la dans Azure pour héberger l’application web. Pour cela, exécutez les commandes suivantes :

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!Note]
   > \<URL de clone Git\> est l’URL du référentiel Git que l’on trouve sur la page **Vue d’ensemble** de l’application web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Ouvrez l’application web pour afficher des données de température et d’humidité en temps réel à partir de votre IoT Hub

Sur la page **Vue d’ensemble** de votre application web, cliquez sur l’URL pour ouvrir l’application web.

![Obtenez l’URL de votre application web Azure](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Vous devriez alors voir les données de température et d’humidité en temps réel à partir de votre IoT Hub.

![Page d’application web Azure affichant l’humidité et la température en temps réel](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

## <a name="next-steps"></a>Étapes suivantes
Vous avez correctement utilisé une application web Azure pour visualiser des données de capteur en temps réel, à partir de votre instance Azure IoT Hub.

Il existe un autre moyen de visualiser ces données depuis Azure IoT Hub. Voir [Visualiser des données de capteur en temps réel depuis Azure IoT Hub, à l’aide de Power BI](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
