<properties 
	pageTitle="Vue d’ensemble des comptes d'intégration et d’Enterprise Integration Pack | Microsoft Azure App Service" 
	description="Découvrez toutes les informations sur les comptes d’intégration, Enterprise Integration Pack et les applications logiques" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>

# Vue d’ensemble des comptes d’intégration

## Qu’est-ce qu'un compte d’intégration ?
Un compte d’intégration est un compte Azure qui permet aux applications Enterprise Integration de gérer des artefacts, y compris des schémas, des mappages, des certificats, des partenaires et des contrats. Chaque application d’intégration que vous créez devra utiliser un compte d’intégration afin d’accéder à un schéma, un mappage ou un certificat, par exemple.

## Création d’un compte d’intégration 
1. Sélectionnez **Parcourir** ![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)
3. Sélectionnez le bouton *Ajouter* dans le menu situé en haut de la page ![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)
4. Entrez le **nom**, sélectionnez l**'abonnement** que vous souhaitez utiliser, créez un nouveau **groupe de ressources** ou sélectionnez un groupe de ressources existant, sélectionnez un **emplacement** où votre compte intégration sera hébergé, sélectionnez un **niveau de tarification**, puis sélectionnez le bouton **Créer**.

  À ce stade, le compte d’intégration sera déployé dans l’emplacement sélectionné. Il devrait se terminer en moins d'une minute. ![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)
5. Actualisez la page. Votre nouveau compte d’intégration apparaîtra dans la liste. Félicitations ! ![](./media/app-service-logic-enterprise-integration-accounts/account-5.png)

## Comment lier un compte d’intégration à une application logique ?
Pour que vos applications logiques puissent accéder à des mappages, des schémas, des contrats et autres artefacts présents sur votre compte d’intégration, vous devez d’abord lier le compte d’intégration à votre application logique.

### Voici les étapes nécessaires pour lier un compte d’intégration à une application logique 

#### Composants requis
- Un compte d’intégration
- Une applications logique

>[AZURE.NOTE]Vérifiez que votre compte d’intégration et votre application logique se trouvent dans le **même emplacement Azure** avant de commencer

1. Sélectionnez le lien **Paramètres** dans le menu de votre application logique ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)
2. Sélectionnez l'élément **Compte d’intégration** dans le panneau Paramètres ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)
3. Sélectionnez le compte d’intégration que vous souhaitez lier à votre application logique dans la liste déroulante **Sélectionner un compte d’intégration** ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)
4. Enregistrez votre travail ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)
5. Une notification vous indique que votre compte d’intégration a été lié à votre application logique et que tous les artefacts de votre compte d’intégration sont désormais disponibles pour votre application logique. ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)

Maintenant que votre compte d’intégration est lié à votre application logique, vous pouvez accéder à votre application logique et utiliser des connecteurs B2B comme XML Validation, Flat file encode/decode ou Transform pour créer des applications avec fonctionnalités B2B.
    
## Comment supprimer un compte d’intégration ?
1. Sélectionnez **Parcourir** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Sélectionnez le **compte d’intégration** que vous souhaitez supprimer ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. Sélectionnez le lien **Supprimer** situé dans le menu ![](./media/app-service-logic-enterprise-integration-accounts/delete.png)
5. Confirmez votre choix

## Comment déplacer un compte d’intégration ?
Vous pouvez facilement déplacer un compte d’intégration vers un nouvel abonnement et un nouveau groupe de ressources. Procédez comme suit si vous devez déplacer votre compte d’intégration :

>[AZURE.IMPORTANT] Vous devrez mettre à jour tous les scripts pour utiliser les nouveaux ID de ressource après avoir déplacé un compte d’intégration.

1. Sélectionnez **Parcourir** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Sélectionnez le **compte d’intégration** que vous souhaitez supprimer ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. Sélectionnez le lien **Déplacer** qui se trouve dans le menu ![](./media/app-service-logic-enterprise-integration-accounts/move.png)
5. Confirmez votre choix

## Étapes suivantes
- [En savoir plus sur les contrats](./app-service-logic-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")


 

<!---HONumber=AcomDC_0706_2016-->