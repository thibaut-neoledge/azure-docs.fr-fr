---
title: "Vue d’ensemble des schémas et d’Enterprise Integration Pack | Microsoft Docs"
description: "Découvrez comment utiliser les schémas avec Enterprise Integration Pack et vos applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2e0419c46e9380eb7e1848524a99501b4961d35c


---
# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>En savoir plus sur les schémas et Enterprise Integration Pack
## <a name="why-use-a-schema"></a>Pourquoi utiliser un schéma ?
Utilisez des schémas pour confirmer que les documents XML que vous recevez sont valides, avec les données attendues dans un format prédéfini. Les schémas sont utilisés pour valider les messages échangés dans un scénario d’entreprise à entreprise (B2B).

## <a name="add-a-schema"></a>Ajout d’un schéma
À partir du portail Azure :  

1. Sélectionnez **Plus de services**.  
   ![Capture du portail Azure, avec « Plus de services » mis en surbrillance](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats.     
   ![Capture d’écran de la zone de recherche de filtre](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Sélectionnez le **compte d’intégration** auquel ajouter le schéma.    
   ![Capture d’écran de la liste des comptes d’intégration](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  
4. Sélectionnez la mosaïque **Schémas**.  
   ![Capture d’écran du compte d’intégration IEP, avec « Schémas » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Ajouter un fichier de schéma d’une taille inférieure à 2 Mo
1. Dans le panneau **Schémas** qui s’affiche (après les étapes précédentes), sélectionnez **Ajouter**.  
   ![Capture d’écran du panneau Schémas, avec le bouton « Ajouter » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  
2. Saisissez un nom pour votre schéma. Ensuite, pour télécharger le fichier de schéma, sélectionnez l’icône de dossier à côté de la zone de texte **Schéma**. Une fois le processus de téléchargement terminé, sélectionnez **OK** .    
   ![Capture d’écran d’« Ajouter un schéma » avec « Petit fichier » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Ajout d’un fichier de schéma supérieur à 2 Mo (jusqu'à un maximum de 8 Mo)
Ce processus varie selon le niveau d’accès du conteneur d’objets blob : **Public** ou **Aucun accès anonyme**. Pour déterminer ce niveau d’accès, dans **Azure Storage Explorer**, sous **Conteneurs d’objets Blob**, sélectionnez le conteneur d’objets blob de votre choix. Sélectionnez **Sécurité**, puis l’onglet **Niveau d’accès**.

1. Si le niveau d’accès de sécurité d’objet blob est **Public**, procédez comme suit.  
   ![Capture d’écran d’Azure Storage Explorer, avec « Conteneurs d’objets Blob », « Sécurité » et « Public » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  
   
    a. Téléchargez le schéma sur le stockage, puis copiez l’URI.  
    ![Capture d’écran du compte de stockage avec l’URI mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  
   
    b. Sélectionnez **Fichier volumineux** dans **Ajouter un schéma** et indiquez l’URI dans la zone de texte **URI du contenu**.  
    ![Capture d’écran des schémas, avec le bouton « Ajouter » et « Fichier volumineux » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  
2. Si le niveau de sécurité de l’accès aux objets blob est défini sur **Pas d’accès anonyme**.  
   ![Capture d’écran d’Azure Storage Explorer, avec « Conteneurs d’objets Blob », « Sécurité » et « Aucun accès anonyme » » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  
   
    a. Chargez le schéma dans le stockage.  
    ![Capture d’écran du compte de stockage](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)
   
    b. Générez un URI de signature d’accès partagé pour le schéma.  
    ![Capture d’écran d’un compte de stockage, avec l’onglet Signatures d'accès partagé mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)
   
    c. Sélectionnez **Fichier volumineux** dans **Ajouter un schéma** et indiquez l’URI de signature d’accès partagé dans la zone de texte **URI du contenu**.  
    ![Capture d’écran des schémas, avec le bouton « Ajouter » et « Fichier volumineux » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  
3. Dans le panneau **Schémas** du compte d’intégration EIP, vous devriez maintenant voir le nouveau schéma.  
   ![Capture d’écran du compte d’intégration IEP, avec « Schémas » et le nouveau schéma mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Modification de schémas
1. Sélectionnez la mosaïque **Schémas**.  
2. Sélectionnez le schéma que vous souhaitez modifier dans le panneau **Schémas** qui s’affiche.
3. Sur le panneau **schémas**, sélectionnez **Modifier**.  
   ![Capture d’écran du panneau Schémas](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Sélectionnez le fichier de schéma que vous souhaitez modifier à l’aide de la boîte de dialogue de sélection de fichier qui s’ouvre.
5. Sélectionnez **Ouvrir** dans le sélecteur de fichiers.  
   ![Capture d’écran de sélecteur de fichier](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Vous recevez une notification indiquant que le téléchargement a réussi.  

## <a name="delete-schemas"></a>Suppression de schémas
1. Sélectionnez la mosaïque **Schémas**.  
2. Sélectionnez le schéma que vous souhaitez supprimer dans le panneau **Schémas** qui s’affiche.  
3. Sur le panneau **schémas**, sélectionnez **Supprimer**.
   ![Capture d’écran du panneau Schémas](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  
4. Pour confirmer votre choix, sélectionnez **Oui**.  
   ![Capture d’écran du message de confirmation « Supprimer le schéma »](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Enfin, notez que la liste des schémas dans le panneau **Schémas** s’actualise et que le schéma que vous avez supprimé n’est plus répertorié.  
   ![Capture d’écran du compte d’intégration EIP, avec « Schémas » mis en surbrillance](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack").  




<!--HONumber=Nov16_HO3-->


