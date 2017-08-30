---
title: "Enregistrer des messages IoT Hub dans le stockage de données Azure | Microsoft Docs"
description: "Utilisez une application de fonction Azure pour enregistrer les messages de votre hub IoT dans un Stockage Table Azure. Les messages IoT Hub contiennent des informations, notamment des données de capteurs, envoyées par l’appareil IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "stockage de données iot, stockage de données de capteur iot"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 06503f9564e00ef62587d02f2da4778974e246c5
ms.contentlocale: fr-fr
ms.lasthandoff: 08/16/2017

---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-table-storage"></a>Enregistrer les messages IoT Hub qui contiennent des données de capteurs dans un Stockage Table Azure

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Contenu

Vous allez découvrir comment créer un compte de stockage Azure et une application de fonction Azure pour stocker des messages IoT Hub dans votre Stockage Table.

## <a name="what-you-do"></a>Procédure

- Créez un compte de stockage Azure.
- Préparez la connexion de votre hub IoT à lire des messages.
- Créez et déployez une application de fonction Azure.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- [Configurez votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) de façon à couvrir les exigences suivantes :
  - Un abonnement Azure actif
  - Un hub IoT associé à votre abonnement 
  - Une application fonctionnelle qui envoie des messages à votre hub IoT

## <a name="create-an-azure-storage-account"></a>Création d'un compte de stockage Azure

1. Sur le [Portail Azure](https://portal.azure.com/), cliquez sur **Nouveau** > **Stockage** > **Compte de stockage** > **Créer**.

2. Entrez les informations nécessaires sur le compte de stockage :

   ![Créer un compte de stockage sur le Portail Azure](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **Name** : nom du compte de stockage. Le nom doit être globalement unique.

   * **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub.

   * **Épingler au tableau de bord** : Sélectionnez cette option pour pouvoir accéder facilement à votre instance IoT Hub à partir du tableau de bord.

3. Cliquez sur **Create**.

## <a name="prepare-your-iot-hub-connection-to-read-messages"></a>Préparer une connexion IoT à lire des messages

IoT Hub expose un point de terminaison intégré compatible avec Event Hub pour permettre aux applications de lire les messages IoT Hub. Dans le même temps, les applications utilisent des groupes de consommateurs pour lire les données provenant du hub IoT. Avant de créer une application de fonction Azure pour lire les données provenant de votre hub IoT, effectuez les opérations suivantes :

- Obtenir la chaîne de connexion du point de terminaison de votre IoT Hub.
- Créer un groupe de consommateurs pour votre IoT Hub.

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>Obtenir la chaîne de connexion du point de terminaison de votre IoT Hub

1. Ouvrez votre IoT Hub.

2. Dans le volet **IoT Hub**, sous **Messagerie**, cliquez sur **Points de terminaison**.

3. Dans le volet de droite, sous **Points de terminaison intégrés**, cliquez sur **Événements**.

4. Dans le volet **Propriétés**, notez les valeurs suivantes :
   - Point de terminaison compatible avec Event Hub
   - Nom compatible avec Event Hub

   ![Obtenir la chaîne de connexion du point de terminaison de votre IoT Hub dans le portail Azure](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

5. Dans le volet **IoT Hub**, sous **Paramètres**, cliquez sur **Stratégies d’accès partagé**.

6. Cliquez sur **iothubowner**.

7. Notez la valeur **Primary key**.

8. Créez la chaîne de connexion du point de terminaison de votre hub IoT, de la façon suivante :

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!NOTE]
   > Remplacez `<Event Hub-compatible endpoint>` et `<Primary key>` par les valeurs que vous avez notées précédemment.

### <a name="create-a-consumer-group-for-your-iot-hub"></a>Créer un groupe de consommateurs pour votre IoT Hub

1. Ouvrez votre IoT Hub.

2. Dans le volet **IoT Hub**, sous **Messagerie**, cliquez sur **Points de terminaison**.

3. Dans le volet de droite, sous **Points de terminaison intégrés**, cliquez sur **Événements**.

4. Dans le volet **Propriétés**, sous **Groupes de consommateurs**, entrez un nom et prenez-en note.

5. Cliquez sur **Enregistrer**.

## <a name="create-and-deploy-an-azure-function-app"></a>Créer et déployer une application de fonction Azure

1. Sur le [Portail Azure](https://portal.azure.com/), cliquez sur **Nouveau** > **Compute** > **Function App** > **Créer**.

2. Entrez les informations nécessaires sur l’application de fonction.

   ![Créer une application de fonction sur le Portail Azure](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   * **Nom de l’application** : nom de l’application de fonction. Le nom doit être globalement unique.

   * **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre IoT Hub.

   * **Compte de stockage** : nom du compte de stockage que vous avez créé.

   * **Épingler au tableau de bord** : cochez cette option pour pouvoir accéder facilement à l’application de fonction à partir du tableau de bord.

3. Cliquez sur **Create**.

4. Une fois l’application de fonction créée, ouvrez-la.

5. Dans l’application de fonction, créez une fonction de la façon suivante :

   a. Cliquez sur **Nouvelle fonction**.

   b. Sélectionnez **JavaScript** pour **Langage** et **Data Processing** (Traitement de données) pour **Scénario**.

   c. Cliquez sur **Créer cette fonction**, puis sur **Nouvelle fonction**.

   d. Sélectionnez **JavaScript** comme langage et **Traitement de données** comme scénario.

   e. Cliquez sur le modèle **EventHubTrigger-JavaScript**.

   f. Entrez les informations nécessaires pour le modèle.

      * **Nommez votre fonction** : nom de la fonction.

      * **Nom du hub d’événements** : nom compatible avec Event Hub que vous avez noté précédemment.

      * **Connexion Event Hub** : pour ajouter la chaîne de connexion du point de terminaison de votre hub IoT que vous avez créée, cliquez sur **Nouveau**.

   g. Cliquez sur **Create**.

6. Configurez une sortie de la fonction de la façon suivante :

   a. Cliquez sur **Intégrer** > **Nouvelle sortie** > **tockage Table Azure** > **Sélectionner**.

      ![Ajouter un Stockage Table à une application de fonction sur le Portail Azure](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)

   b. Entrez les informations nécessaires.

      * **Nom du paramètre de table** : choisissez `outputTable`, qui sera utilisé dans le code de la fonction Azure.
      
      * **Nom de la table** : utilisez `deviceData`.

      * **Connexion au compte de stockage** : cliquez sur **Nouveau**, puis sélectionnez ou entrez votre compte de stockage. Si le compte de stockage ne s’affiche pas, consultez la page [Exigences relatives aux comptes de stockage](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).
      
   c. Cliquez sur **Enregistrer**.

7. Sous **Déclencheurs**, cliquez sur **Azure Event Hub (eventHubMessages)**.

8. Sous **Groupe de consommateurs du hub d’événements**, entrez le nom du groupe de consommateurs créé précédemment, puis cliquez sur **Enregistrer**.

9. Cliquez sur la fonction que vous avez créée sur la gauche, puis sur **Afficher les fichiers** sur la droite.

10. Remplacez le code de `index.js` par le suivant :

   ```javascript
   'use strict';

   // This function is triggered each time a message is received in the IoT hub.
   // The message payload is persisted in an Azure storage table
 
   module.exports = function (context, iotHubMessage) {
    context.log('Message received: ' + JSON.stringify(iotHubMessage));
    var date = Date.now();
    var partitionKey = Math.floor(date / (24 * 60 * 60 * 1000)) + '';
    var rowKey = date + '';
    context.bindings.outputTable = {
     "partitionKey": partitionKey,
     "rowKey": rowKey,
     "message": JSON.stringify(iotHubMessage)
    };
    context.done();
   };
   ```

11. Cliquez sur **Enregistrer**.

Vous avez à présent créé l’application de fonction. Elle stocke les messages reçus par votre hub IoT dans votre Stockage Table.

> [!NOTE]
> Vous pouvez utiliser le bouton **Exécuter** pour tester l’application de fonction. Lorsque vous cliquez sur **Exécuter**, le message de test est envoyé à votre IoT Hub. À l’arrivée du message, l’application de fonction devrait démarrer, puis enregistrer le message dans votre Stockage Table. Le volet **Journaux** enregistre les détails du processus.

## <a name="verify-your-message-in-your-table-storage"></a>Vérifier votre message dans votre stockage de table

1. Exécutez l’exemple d’application sur votre appareil pour envoyer des messages à votre IoT Hub.

2. [Téléchargez et installez l’Explorateur de Stockage Azure](http://storageexplorer.com/).

3. Ouvrez l’Explorateur de Stockage, cliquez sur **Ajouter un compte Azure** > **Se connecter**, puis connectez-vous à votre compte Azure.

4. Cliquez sur votre abonnement Azure > **Comptes de stockage** > votre compte de stockage > **Tables** > **deviceData**.

   Vous devriez voir les messages envoyés par votre appareil à votre IoT Hub consigné dans la table `deviceData`.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé votre compte de stockage Azure et votre application de fonction Azure, qui stocke les messages reçus par votre hub IoT dans votre Stockage Table Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

