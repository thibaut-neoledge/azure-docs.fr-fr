---
title: "Créer, lier, supprimer ou déplacer un compte d’intégration dans Azure Logic Apps | Microsoft Docs"
description: "Comment créer un compte d’intégration et l’associer à vos applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: dca762854f22721de76f69cbc28c0377d563fe28
ms.openlocfilehash: e26271a1291fc76e3ea8f93a7aa9ab8c811ff604


---

# <a name="what-is-an-integration-account"></a>Qu’est-ce qu'un compte d’intégration ?
Un compte d’intégration permet aux applications Enterprise Integration de gérer des artefacts, y compris des schémas, des mappages, des certificats, des partenaires et des contrats. Toutes les applications d’intégration que vous créez utilisent un compte d’intégration pour accéder à ces schémas, mappages, certificats, et ainsi de suite.

## <a name="create-an-integration-account"></a>Création d’un compte d’intégration
1. Sélectionnez **Parcourir** :   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. Saisissez **intégration** dans la zone de recherche de filtre, puis sélectionnez **Comptes d’intégration** dans la liste des résultats :     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. Cliquez sur le bouton *Ajouter* dans le menu situé en haut de la page :      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. Entrez le **nom**, sélectionnez **l’abonnement** à utiliser, créez un nouveau **groupe de ressources** ou sélectionnez-en un déjà existant, sélectionnez un **emplacement** où votre compte d’intégration sera hébergé, puis un **niveau tarifaire**, et enfin cliquez sur le bouton **Créer**.   
   
   À ce stade, le compte d’intégration sera déployé dans l’emplacement sélectionné. Il devrait se terminer en moins d'une minute.    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. Actualisez la page. Votre nouveau compte d’intégration apparaît désormais dans la liste :  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Ensuite, liez le compte d’intégration que vous venez de créer à votre application logique. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Lier un compte d’intégration à une application logique
Pour que vos applications logiques puissent accéder à des mappages, des schémas, des contrats et autres artefacts de votre compte d’intégration, liez le compte d’intégration à votre application logique.

#### <a name="prereqs"></a>Conditions préalables
* Un compte d’intégration
* Une application logique

> [!NOTE] 
> Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le **même emplacement Azure** avant de commencer.

1. Cliquez sur le lien **Paramètres** dans le menu de votre application logique :  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. Sélectionnez l’élément **Compte d’intégration** dans le panneau Paramètres :  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. Sélectionnez le compte d’intégration que vous souhaitez lier à votre application logique dans la liste déroulante **Sélectionner un compte d’intégration** :  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. Enregistrez votre travail :  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. Une notification vous indique que votre compte d’intégration a été lié à votre application logique et que tous les artefacts de votre compte d’intégration sont désormais disponibles pour votre application logique :  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

Maintenant que votre compte d’intégration est lié à votre application logique, vous pouvez utiliser les connecteurs B2B dans votre application logique. Parmi les connecteurs B2B les plus courants figurent la validation XML, et l’encodage et le décodage de fichiers plats.  

## <a name="how-to-delete-an-integration-account"></a>Comment supprimer un compte d’intégration ?
1. Sélectionnez **Parcourir** :  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Saisissez **intégration** dans la zone de recherche de filtre, puis sélectionnez **Comptes d’intégration** dans la liste des résultats :     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Sélectionnez le **compte d’intégration** que vous voulez supprimer :  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Cliquez sur le lien **Supprimer** situé dans le menu :   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. Confirmez votre choix.    

## <a name="how-to-move-an-integration-account"></a>Comment déplacer un compte d’intégration ?
Vous pouvez facilement déplacer un compte d’intégration vers un nouvel abonnement et un nouveau groupe de ressources. Procédez comme suit si vous devez déplacer votre compte d’intégration :

> [!IMPORTANT]
> Quand vous déplacez un compte d’intégration, vous devez mettre à jour tous les scripts pour qu’ils utilisent les nouveaux ID de ressource.

1. Sélectionnez **Parcourir** :  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Saisissez **intégration** dans la zone de recherche de filtre, puis sélectionnez **Comptes d’intégration** dans la liste des résultats :     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Sélectionnez le **compte d’intégration** que vous voulez supprimer :  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Cliquez sur le lien **Déplacer** qui se trouve dans le menu :   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. Confirmez votre choix.    

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  




<!--HONumber=Jan17_HO5-->


