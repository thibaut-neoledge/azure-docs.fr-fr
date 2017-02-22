---
title: "Présentation des partenaires et d’Enterprise Integration Pack | Microsoft Docs"
description: "Découvrez comment utiliser les partenaires avec Enterprise Integration Pack et vos applications logiques"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 549dd716df6ee64892ef33ffe4d42751d8662ccb


---
# <a name="learn-about-partners-and-enterprise-integration-pack"></a>En savoir plus sur les partenaires et Enterprise Integration Pack
## <a name="overview"></a>Vue d'ensemble
Avant de pouvoir créer un partenaire, vous et l’organisation avec laquelle vous avez l’intention de vous associer devez partager des informations qui vous aideront à identifier et à valider les messages que vous enverrez. Après cette conversation et une fois que vous êtes prêt à lancer votre relation commerciale, vous pouvez créer un *partenaire* dans votre compte d’intégration.

## <a name="what-is-a-partner"></a>Qu’est-ce qu’un partenaire ?
Les partenaires sont les entités qui participent à la messagerie et aux transactions d’entreprise à entreprise (B2B). 

## <a name="how-are-partners-used"></a>Comment les partenaires sont-ils utilisés ?
Les partenaires sont utilisés pour créer des contrats. Un contrat définit les détails des messages échangés entre les partenaires. 

Avant de pouvoir créer un contrat, vous devez avoir ajouté au moins deux partenaires à votre compte d’intégration. L’un des partenaires du contrat doit être votre organisation. Le partenaire représentant votre organisation est appelé le **partenaire hôte**. Le deuxième partenaire représente l’autre organisation avec laquelle votre organisation échange des messages. Le deuxième partenaire est appelé le **partenaire invité**. Le partenaire invité peut être une autre entreprise ou même un service au sein de votre organisation.  

Après avoir ajouté les partenaires, vous utilisez ceux-ci pour créer un contrat. 

Les paramètres de réception et d’envoi sont basés du point de vue du partenaire hébergé. Par exemple, les paramètres de réception dans un contrat déterminent la façon dont le partenaire hébergé reçoit les messages envoyés à partir d’un partenaire invité. De même, les paramètres d’envoi du contrat indiquent la façon dont le partenaire hébergé envoie des messages au partenaire invité.

## <a name="how-to-create-a-partner"></a>Création d’un partenaire
À partir du portail Azure :  

1. Sélectionnez **Parcourir**.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Comptes d’intégration** dans la liste des résultats     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Sélectionnez le **compte d’intégration** auquel vous ajouterez les partenaires  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Sélectionnez la mosaïque **Partenaires**  
   ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)  
5. Sélectionnez le bouton **Ajouter** dans le panneau Partenaires qui s’affiche  
   ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)  
6. Entrez un **nom** pour votre partenaire, sélectionnez le **qualificateur**, puis entrez une **valeur**. La valeur est utilisée pour identifier les documents qui entrent dans vos applications.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)  
7. Sélectionnez l’icône de notification en forme de *cloche* pour afficher la progression du processus de création du partenaire.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)  
8. Sélectionnez la mosaïque **Partenaires**. Cette action actualise la mosaïque. Le nombre de partenaires doit augmenter, indiquant que le nouveau partenaire a été ajouté avec succès.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)  
9. Une fois la mosaïque Partenaires sélectionnée, le nouveau partenaire doit également s’afficher dans le panneau Partenaires.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)  

## <a name="how-to-edit-a-partner"></a>Modification d’un partenaire
Suivez ces étapes pour modifier un partenaire qui existe déjà dans votre compte d’intégration :  

1. Sélectionnez la mosaïque **Partenaires**  
2. Sélectionnez le partenaire à modifier lorsque le panneau Partenaires s’ouvre  
3. Sur la mosaïque **Update Partner** (Mettre le partenaire à jour), apportez les modifications nécessaires  
4. Si vous êtes satisfait de vos modifications, sélectionnez le lien **Enregistrer**. Dans le cas contraire, sélectionnez le lien **Ignorer** pour annuler vos modifications.  
   ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)  

## <a name="how-to-delete-a-partner"></a>Suppression d’un partenaire
1. Sélectionnez la mosaïque **Partenaires**  
2. Sélectionnez le partenaire à modifier lorsque le panneau Partenaires s’ouvre  
3. Sélectionnez le lien **Supprimer**    
   ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)   

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  




<!--HONumber=Jan17_HO3-->


