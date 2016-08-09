<properties 
	pageTitle="Vue d’ensemble des mappages Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
	description="Découvrez comment utiliser les mappages avec Enterprise Integration Pack et vos applications logiques" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# En savoir plus sur les mappages et Enterprise Integration Pack

## Vue d'ensemble
Enterprise Integration utilise des mappages pour convertir les données XML d’un format vers un autre format.

## Qu’est-ce qu’un mappage ?
Un mappage est un document XML qui indique quelles données d'un document doivent être converties vers un autre format.

## Pourquoi utiliser des mappages ?
Imaginons que vous recevez régulièrement des commandes ou des factures B2B de la part d'un client qui utilise le format AAAMMJJ pour les dates. Mais dans votre entreprise, les dates sont enregistrées au format MMJJAAA. Vous pouvez utiliser un mappage pour *convertir* le format de date AAAMMJJ vers MMJJAAA avant d'enregistrer les détails de la commande ou de la facture dans votre base de données clients.

## Comment charger un mappage ?
À partir du portail Azure :
1. Sélectionnez **Parcourir** ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Integration Accounts** (Comptes d’intégration) dans la liste des résultats ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. Sélectionnez le **compte d’intégration** auquel vous ajouterez le mappage ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  Sélectionnez la mosaïque **Mappages** ![](./media/app-service-logic-enterprise-integration-maps/map-1.png)
5. Sélectionnez le bouton **Ajouter** dans le panneau Mappages qui s’affiche ![](./media/app-service-logic-enterprise-integration-maps/map-2.png)
6. Entrez un **nom** pour votre mappage, puis sélectionnez l’icône de dossier située à droite de la zone de texte **Mappage** pour télécharger le fichier de mappage. Une fois le processus de téléchargement terminé, sélectionnez le bouton **OK**. ![](./media/app-service-logic-enterprise-integration-maps/map-3.png)
7. Le mappage est désormais ajouté à votre compte d’intégration. Vous recevrez une notification à l’écran indiquant si l'ajout du fichier de mappage a réussi ou échoué. Après avoir reçu la notification, sélectionnez la mosaïque **Mappages** ; le mappage que vous venez d'ajouter apparaît dans le panneau Mappages : ![](./media/app-service-logic-enterprise-integration-maps/map-4.png)

## Comment modifier un mappage ?
Pour modifier un mappage, vous devez télécharger un nouveau fichier de mappage intégrant les modifications souhaitées. Vous pouvez d’abord télécharger le mappage puis le modifier.

Procédez comme suit pour télécharger un nouveau mappage remplaçant un mappage existant :
1. Sélectionnez la mosaïque **Mappages**
2. Sélectionnez le mappage que vous souhaitez modifier lorsque le panneau Mappages s'ouvre
3. Dans le panneau **Mappages**, sélectionnez le lien **Mettre à jour** ![](./media/app-service-logic-enterprise-integration-maps/edit-1.png)
4. Sélectionnez le fichier de mappage que vous souhaitez télécharger à l’aide de la boîte de dialogue de sélection de fichier qui s’ouvre, puis choisissez **Ouvrir** dans le sélecteur de fichiers ![](./media/app-service-logic-enterprise-integration-maps/edit-2.png)
5. Vous recevrez un message de notification une fois le mappage téléchargé.

## Comment supprimer un mappage ?
1. Sélectionnez la mosaïque **Mappages**
2. Sélectionnez le mappage que vous souhaitez supprimer lorsque le panneau Mappages s'ouvre
3. Sélectionnez le lien **Supprimer** ![](./media/app-service-logic-enterprise-integration-maps/delete.png)
4. Confirmez que vous voulez vraiment supprimer le mappage. ![](./media/app-service-logic-enterprise-integration-maps/delete-confirmation-1.png)

## Étapes suivantes
- [En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")
- [En savoir plus sur les contrats](./app-service-logic-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")
- [En savoir plus sur les transformations](./app-service-logic-enterprise-integration-transform.md "Découvrez les transformations d’intégration d’entreprise")

<!---HONumber=AcomDC_0727_2016-->