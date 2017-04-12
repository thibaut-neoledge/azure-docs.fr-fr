---
title: "Enregistrer les messages IoT Hub dans le stockage de données Azure | Microsoft Docs"
description: "Utilisez une application Azure Function App pour enregistrer les messages IoT Hub dans un stockage de table Azure. Les messages IoT Hub contiennent des informations telles que les données de capteur envoyées par votre appareil IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "stockage de données iot, stockage de données de capteur iot"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: ef5d4af5d5dad2b548ab01be5ba9c4769b21416a
ms.lasthandoff: 04/12/2017


---
# <a name="save-iot-hub-messages-that-contain-information-like-sensor-data-to-azure-table-storage"></a>Enregistrer les messages IoT Hub qui contiennent des informations telles que des données de capteur à un stockage de table Azure

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-will-learn"></a>Contenu

Vous allez découvrir comment créer un compte de stockage Azure et une application Azure Function App pour stocker les messages IoT Hub dans un stockage de table Azure.

## <a name="what-you-will-do"></a>Procédure à suivre

- Créez un compte de stockage Azure.
- Préparer la connexion IoT Hub pour la lecture des messages.
- Créer et déployer une application Azure Function App.

## <a name="what-you-will-need"></a>Éléments requis

- Avoir suivi le didacticiel [Connecter la carte ESP8266 à Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md), qui vous permettra de disposer des éléments requis suivants :
  - Un abonnement Azure actif.
  - Un Azure IoT Hub associé à votre abonnement.
  - Une application fonctionnelle qui envoie des messages à votre Azure IoT Hub.

## <a name="create-an-azure-storage-account"></a>Création d'un compte de stockage Azure

1. Dans le portail Azure, cliquez sur **Nouveau** > **Stockage** > **ompte de stockage**.
1. Entrez les informations nécessaires pour le compte de stockage :

   ![Création d’un compte de stockage dans le portail Azure](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   **Name** : nom du compte de stockage. Le nom doit être globalement unique.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub.

   **Épingler au tableau de bord** : cochez cette option pour pouvoir accéder facilement à votre IoT Hub à partir du tableau de bord.
1. Cliquez sur **Create**.

## <a name="prepare-for-iot-hub-connection-to-read-messages"></a>Préparation de la connexion IoT Hub pour la lecture des messages

IoT Hub expose un point de terminaison intégré compatible avec Event Hub pour permettre aux applications de lire les messages IoT Hub. Dans le même temps, les applications utilisent des groupes de consommateurs pour lire les données provenant de IoT Hub. Avant de créer une application Azure Function App pour lire les données provenant de votre IoT Hub, vous devez :

- Obtenir la chaîne de connexion du point de terminaison de votre IoT Hub.
- Créer un groupe de consommateurs pour votre IoT Hub.

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>Obtenir la chaîne de connexion du point de terminaison de votre IoT Hub

1. Ouvrez votre IoT Hub.
1. Dans le volet **IoT Hub**, cliquez sur **Points de terminaison** sous **MESSAGERIE**.
1. Dans le volet de droite, cliquez sur **Événements** sous **Built-in endpoints** (Points de terminaison intégrés).
1. Dans le volet **Propriétés**, notez les valeurs suivantes :
   - Point de terminaison compatible Event Hub
   - Event Hub-compatible name (Nom compatible Event Hub)

   ![Obtenir la chaîne de connexion du point de terminaison de votre IoT Hub dans le portail Azure](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

1. Dans le volet **IoT Hub**, cliquez sur **Stratégies d’accès partagé** sous **PARAMÈTRES**.
1. Cliquez sur **iothubowner**.
1. Prenez note de la valeur de **Clé primaire**.
1. Constituez la chaîne de connexion du point de terminaison de votre IoT Hub comme suit :

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!Note]
   > Remplacez `<Event Hub-compatible endpoint>` et `<Primary key>` par les valeurs que vous avez notées.

### <a name="create-a-consumer-group-for-your-iot-hub"></a>Créer un groupe de consommateurs pour votre IoT Hub

1. Ouvrez votre IoT Hub.
1. Dans le volet **IoT Hub**, cliquez sur **Points de terminaison** sous **MESSAGERIE**.
1. Dans le volet de droite, cliquez sur **Événements** sous **Built-in endpoints** (Points de terminaison intégrés).
1. Dans le volet **Propriétés**, entrez un nom sous **Groupes de consommateurs** et prenez note de ce nom.
1. Cliquez sur **Save**.

## <a name="create-and-deploy-an-azure-function-app"></a>Créer et déployer une application Azure Function App

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Nouveau** > **Compute** > **Function App**.
1. Entrez les informations nécessaires pour l’application Function App.

   ![Créer une application Function App dans le Portail Azure](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   **Nom de l’application** : nom de l’application Function App. Le nom doit être globalement unique.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub.

   **Compte de stockage** : nom du compte de stockage que vous avez créé.

   **Épingler au tableau de bord** : cochez cette option pour pouvoir accéder facilement à l’application Function App à partir du tableau de bord.
1. Cliquez sur **Create**.
1. Ouvrez l’application Function App une fois la création terminée.
1. Créez une nouvelle fonction dans l’application Function App.
   1. Cliquez sur **Nouvelle fonction**.
   1. Sélectionnez **JavaScript** pour **Langage** et **Data Processing** (Traitement de données) pour **Scénario**.
   1. Cliquez sur le modèle **EventHubTrigger-JavaScript**.
   1. Entrez les informations nécessaires pour le modèle.

      **Nommez votre fonction** : nom de la fonction.

      **Nom du hub d’événements** : nom compatible Event Hub que vous avez noté.

      **Event Hub connection** (Connexion Event Hub) : cliquez sur Nouveau pour ajouter la chaîne de connexion du point de terminaison de votre IoT Hub que vous avez constituée.
   1. Cliquez sur **Create**.
1. Configurez une sortie de la fonction.
   1. Cliquez sur **Intégrer** > **Nouvelle sortie** > **tockage Table Azure** > **Sélectionner**.

      ![Ajouter un stockage de table à votre application Function App dans le portail Azure](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)
   1. Entrez les informations nécessaires.

      **Nom de la table** : utilisez `deviceData` pour le nom.

      **Connexion au compte de stockage** : cliquez sur **Nouveau** et sélectionnez votre compte de stockage.
   1. Cliquez sur **Save**.
1. Sous **Déclencheurs**, cliquez sur **Azure Event Hub (myEventHubTrigger)**.
1. Sous **Groupe de consommateurs du hub d’événements**, entrez le nom du groupe de consommateurs créé précédemment, puis cliquez sur **Enregistrer**.
1. Cliquez sur **Développer**, puis sur **Afficher les fichiers**.
1. Cliquez sur **Ajouter** pour ajouter un nouveau fichier nommé `package.json`, collez les informations suivantes, puis cliquez sur **nregistrer**.

   ```json
   {
      "name": "iothub_save_message_to_table",
      "version": "0.0.1",
      "private": true,
      "main": "index.js",
      "author": "Microsoft Corp.",
      "dependencies": {
         "azure-iothub": "1.0.9",
         "azure-iot-common": "1.0.7",
         "moment": "2.14.1"
      }
   }
   ```
1. Remplacez le code de `index.js` par le code suivant, puis cliquez sur **Enregistrer**.

   ```javascript
   'use strict';

   // This function is triggered each time a message is revieved in the IoTHub.
   // The message payload is persisted in an Azure Storage Table
   var moment = require('moment');

   module.exports = function (context, iotHubMessage) {
      context.log('Message received: ' + JSON.stringify(iotHubMessage));
      context.bindings.outputTable = {
      "partitionKey": moment.utc().format('YYYYMMDD'),
         "rowKey": moment.utc().format('hhmmss') + process.hrtime()[1] + '',
         "message": JSON.stringify(iotHubMessage)
      };
      context.done();
   };
   ```
1. Cliquez sur **Function app settings**(Paramètres Function App) > **Ouvrir la console de développement**.

   Vous devriez vous trouver dans le dossier `wwwroot` de l’application Function App.
1. Accédez au dossier de la fonction en exécutant la commande suivante :

   ```bash
   cd <your function name>
   ```
1. Installez le package npm en exécutant la commande suivante :

   ```bash
   npm install
   ```

   > [!Note]
   > L’installation peut prendre un certain temps.

L’application Function App est maintenant créée. Elle stocke les messages reçus par votre IoT Hub dans votre stockage de table Azure.

> [!Note]
> Vous pouvez utiliser le bouton **xécuter** pour tester l’application Function App. Lorsque vous cliquez sur **Exécuter**, le message de test est envoyé à votre IoT Hub. À l’arrivée du message, l’application Function App devrait démarrer, puis enregistrer le message dans votre stockage de table Azure. Le volet **Journaux** enregistre les détails du processus.

## <a name="verify-your-message-in-your-table-storage"></a>Vérifier votre message dans votre stockage de table

1. Exécutez l’exemple d’application sur votre appareil pour envoyer des messages à votre IoT Hub.
1. [Téléchargez et installez Microsoft Azure Storage Explorer](http://storageexplorer.com/).
1. Ouvrez Microsoft Azure Storage Explorer, cliquez sur **Add an Azure Account** (Ajouter un compte Azure) > **Sign in** (Se connecter), puis connectez-vous à votre compte Azure.
1. Cliquez sur votre abonnement Azure > **Comptes de stockage** > votre compte de stockage > **Tables** > **deviceData**.

   Vous devriez voir les messages envoyés par votre appareil à votre IoT Hub consigné dans la table `deviceData`.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé votre compte de stockage Azure et votre application Azure Function App pour stocker les messages que votre IoT Hub reçoit dans votre stockage de table Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
