---
title: Traiter des messages EDI par lots (groupe ou collection de messages) - Azure Logic Apps | Microsoft Docs
description: "Envoyer des messages EDI à traiter par lots dans les applications logiques"
keywords: lot, traitement par lots, encodage par lots
author: divswa
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: LADocs; estfan; divswa
ms.openlocfilehash: 8c794b8c919fe286c3917927a44d64aa84771f28
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="send-x12-messages-in-batch-to-trading-partners"></a>Envoyer des messages X12 par lots à des partenaires commerciaux

Dans les scénarios d’entreprise à entreprise (B2B), les partenaires échangent souvent des messages par groupes ou par lots. Pour envoyer des messages par groupes ou par lots à vos partenaires commerciaux, vous pouvez créer un lot contenant plusieurs éléments, puis utiliser l’opération de traitement par lots X12 pour traiter ces éléments en tant que lot.


Le traitement par lots des messages X12 utilise, à l’instar d’autres messages, un déclencheur et une opération de traitement par lots. De plus, avec X12, le lot passe par une étape d’encodage X12 avant d’être envoyé au partenaire ou vers une autre destination. Pour plus d’informations sur le déclencheur et l’opération de traitement par lots, consultez [Traiter par lots des messages](logic-apps-batch-process-send-receive-messages.md).

Cette rubrique explique comment vous pouvez traiter des messages X12 sous forme de lot en effectuant les tâches suivantes :
* [Créer une application logique destinée à recevoir des éléments et créer un lot](#receiver). Cette application logique « réceptrice » exécute les actions suivantes :
 
   * Elle spécifie le nom du lot et les critères de déclenchement à remplir avant le traitement des éléments sous forme de lot.

   * Elle traite ou encode les éléments du lot par le biais de l’accord X12 spécifié ou des identités des partenaires.

* [Créer une application logique qui envoie les éléments vers un lot](#sender). Cette application logique « expéditrice » spécifie l’emplacement où envoyer les éléments à traiter par lots, qui doit se trouver dans une application logique réceptrice existante.


## <a name="prerequisites"></a>Composants requis

Pour suivre cet exemple, vous avez besoin de ce qui suit :

* Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez [commencer par créer gratuitement un compte Azure](https://azure.microsoft.com/free/). Sinon, vous pouvez souscrire à un [abonnement de type paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/).

* Un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md) déjà défini et associé à votre abonnement Azure

* Au moins deux [partenaires](logic-apps-enterprise-integration-partners.md) que vous avez déjà définis dans votre compte d’intégration. Assurez-vous que chaque partenaire utilise le qualificateur X12 (Code alphanumérique standard) dans les propriétés du partenaire en tant qu’identité d’entreprise.

* Un [contrat X12](logic-apps-enterprise-integration-x12.md) déjà défini dans votre compte d’intégration

<a name="receiver"></a>

## <a name="create-a-logic-app-that-receives-x12-messages-and-creates-a-batch"></a>Créer une application logique destinée à recevoir des éléments X12 et créer un lot

Avant de pouvoir envoyer des messages vers un lot, vous devez d’abord créer une application logique « réceptrice » à l’aide du déclencheur **Lot**. De cette façon, vous pouvez sélectionner cette application logique réceptrice lorsque vous créez l’application logique expéditrice. Pour l’application réceptrice, vous devez spécifier le nom du lot, les critères de déclenchement, l’accord X12 et d’autres paramètres. 


1. Dans le [portail Azure](https://portal.azure.com), créez une application logique et nommez-la « BatchX12Messages ».

2. Dans le Concepteur d'applications logiques, ajoutez le déclencheur **Lot** qui démarre le flux de travail de votre application logique. Dans la zone de recherche, entrez « lot » comme filtre. Sélectionnez le déclencheur **Lot – Traiter les messages par lots**.

   ![Ajout du déclencheur Lot](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-receiver-trigger.png)

3. Fournissez un nom pour le lot, puis spécifiez des critères pour son déclenchement, par exemple :

   * **Nom du lot** : nom utilisé pour identifier le lot (« TestBatch » dans cet exemple).

   * **Critères de déclenchement** : critères de déclenchement du lot, liés au nombre de messages, à la planification ou aux deux.
   
     ![Détails à fournir concernant le déclencheur Lot](./media/logic-apps-batch-process-send-receive-messages/receive-batch-release-criteria.png)

   * **Nombre de messages** : nombre de messages devant être contenus dans un lot avant le déclenchement du traitement (« 5 » dans cet exemple).

     ![Détails à fournir concernant le déclencheur Lot](./media/logic-apps-batch-process-send-receive-messages/receive-batch-count-based.png)

   * **Planification** : planification du déclenchement du traitement du lot, « toutes les 10 minutes » dans cet exemple.

     ![Détails à fournir concernant le déclencheur Lot](./media/logic-apps-scenario-EDI-send-batch-messages/receive-batch-schedule-based.png)


4. Ajoutez une autre action qui encode les messages groupés ou par lots et crée un message traité par lot X12. 

   a. Sélectionnez **+ Nouvelle étape** > **Ajouter une action**.

   b. Dans la zone de recherche, entrez « X12 batch » en tant que filtre et sélectionnez une action pour **X12 - Batch Encode** (encodage par lots X12). Comme pour le connecteur d’encodage X12, il existe plusieurs variantes de l’action d’encodage par lots. Vous pouvez sélectionner l’une d’elles.

   ![Sélection de l’action d’encodage par lots X12](./media/logic-apps-scenario-EDI-send-batch-messages/add-batch-encode-action.png)
   
5. Définissez les propriétés de l’action que vous venez d’ajouter.

   * Dans la zone **Nom de l’accord X12**, sélectionnez l’accord approprié dans la liste déroulante. Si la liste est vide, assurez-vous que vous avez bien créé une connexion à votre compte d’intégration.

   * Dans la zone **BatchName**, sélectionnez le champ **Nom du lot** dans la liste de contenu dynamique.
   
   * Dans la zone **PartitionName**, sélectionnez le champ **Nom de la partition** dans la liste de contenu dynamique.

   * Dans la zone **Éléments**, sélectionnez les **éléments traités par lots** dans la liste de contenu dynamique.

   ![Détails de l’action d’encodage par lots](./media/logic-apps-scenario-EDI-send-batch-messages/batch-encode-action-details.png)

6. À des fins de test, ajoutez une action HTTP pour envoyer le message traité par lot au [service RequestBin](https://requestbin.fullcontact.com/). 

   1. Dans la zone de recherche, entrez « HTTP » en tant que filtre. Sélectionnez cette action : **HTTP - HTTP**
    
      ![Sélection de l’action HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action.png)

   2. Dans la liste **Méthode**, sélectionnez **POST**. Dans la zone **Uri**, générez un URI pour votre RequestBin et entrez cet URI. Dans la zone **Corps**, dans la liste dynamique qui s’ouvre, sélectionnez le champ **Corps** dans la section **Encodage Batch par nom de contrat**. Si le champ **Corps** n’apparaît pas, cliquez sur **Voir plus** en regard du titre de section **Encodage Batch par nom de contrat**.

      ![Saisie des détails de l’action HTTP](./media/logic-apps-scenario-EDI-send-batch-messages/batch-receive-add-http-action-details.png)

7.  Maintenant que vous avez créé l’application logique réceptrice, enregistrez-la.

    ![Enregistrer votre application logique](./media/logic-apps-scenario-EDI-send-batch-messages/save-batch-receiver-logic-app.png)

<a name="sender"></a>

## <a name="create-a-logic-app-that-sends-x12-messages-to-a-batch"></a>Créer une application logique qui envoie des messages X12 vers un lot

À présent, créez une ou plusieurs applications logiques qui envoient des éléments vers le lot défini par l’application logique réceptrice. Pour l’application expéditrice, vous spécifiez l’application logique réceptrice, le nom du lot, le contenu du message et tout autre paramètre nécessaire. Vous pouvez éventuellement fournir une clé de partition unique pour diviser le lot en sous-ensembles dont le but est de collecter les éléments associés à cette clé.

Les applications logiques expéditrices doivent savoir où envoyer les éléments. Les applications logiques réceptrices, quant à elles, n’ont rien à savoir sur les applications expéditrices.


1. Créez une autre application logique et nommez-la « X12MessageSender ». Ajoutez le déclencheur suivant à votre application logique : **Request / Response - Request** 
   
   ![Ajout du déclencheur Request](./media/logic-apps-scenario-EDI-send-batch-messages/add-request-trigger-sender.png)

2. Ajoutez une nouvelle étape pour envoyer des messages vers un lot.

   1. Sélectionnez **+ Nouvelle étape** > **Ajouter une action**.

   2. Dans la zone de recherche, entrez « lot » comme filtre. 

3. Sélectionnez l’action **Envoyer les messages au lot - Choisir un workflow Logic Apps avec déclencheur de lot**.

   ![Sélection de l’option « Envoyer les messages au lot »](./media/logic-apps-scenario-EDI-send-batch-messages/send-messages-batch-action.png)

4. Sélectionnez à présent l’application logique « BatchX12Messages » créée précédemment, qui s’affiche désormais en tant qu’action.

   ![Sélection de l’application logique « BatchReceiver »](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-receiver.png)

   > [!NOTE]
   > La liste affiche également toutes les autres applications logiques qui sont associées à un déclencheur de lot.

5. Définissez les propriétés du lot.

   * **Nom du lot** : nom défini par l’application logique réceptrice, (« TestBatch » dans cet exemple) qui est validé lors de l’exécution.

     > [!IMPORTANT]
     > Veillez à ne pas modifier le nom du lot, car il doit correspondre au nom de lot spécifié par l’application logique réceptrice.
     > Si vous modifiez son nom, l’application logique expéditrice échouera.

   * **Contenu du message** : contenu du message que vous souhaitez envoyer vers le lot
   
   ![Définition des propriétés du lot](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-select-batch-properties.png)

6. Enregistrez votre application logique. Votre application logique expéditrice doit désormais ressembler à ceci :

   ![Enregistrement de l’application logique](./media/logic-apps-scenario-EDI-send-batch-messages/send-batch-finished.png)

## <a name="test-your-logic-apps"></a>Tester les applications logiques

Pour tester votre solution de traitement par lots, publiez des messages X12 dans votre application logique expéditrice à partir de [Postman](https://www.getpostman.com/postman) ou d’un outil similaire. Vous devriez bientôt commencer à recevoir des messages X12, soit sous la forme d’un lot de cinq éléments, soit toutes les 10 minutes, dans votre RequestBin, tous avec la même clé de partition.

## <a name="next-steps"></a>Étapes suivantes

* [Traiter par lots des messages](logic-apps-batch-process-send-receive-messages.md) 
* [Créer une application sans serveur dans Visual Studio avec Azure Logic Apps et Azure Functions](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Gestion des exceptions et journalisation des erreurs pour les applications logiques](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
