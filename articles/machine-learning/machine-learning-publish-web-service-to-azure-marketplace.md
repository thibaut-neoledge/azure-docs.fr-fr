<properties 
	pageTitle="Publication du service web Azure Machine Learning sur Azure Marketplace | Azure" 
	description="Publication du service web Azure Machine Learning sur Azure Marketplace" 
	services="machine-learning" 
	documentationCenter="" 
	authors="luisca" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/03/2014" 
	ms.author="luisca"/>

# Publication des services web Azure ML sur Azure Marketplace 

Dans ce document :

- [Introduction]
- [Présentation du processus de publication]
- [Consignes pour la publication sur Azure Marketplace]
- [Options spécifiques à l'apprentissage automatique] 

<!--Anchors-->
[Introduction]: #introduction
[Présentation du processus de publication]: #overview-of-the-publishing-process
[Consignes pour la publication sur Azure Marketplace]: #guidelines-for-publishing-to-azure-marketplace
[Options spécifiques à l'apprentissage automatique]: #machine-learning-specific-options 

## Introduction

Azure Marketplace offre la possibilité de publier des services web Azure Machine Learning sous forme de services payants ou gratuits que des clients externes pourront consommer. Ce document propose une présentation du processus, avec des liens vers les consignes pour vous aider à démarrer. À l'aide de ce processus, vous pouvez mettre vos services web à disposition d'autres développeurs, qui pourront les consommer dans leurs applications.

## Présentation du processus de publication 

Les étapes suivantes illustrent la publication d'un service web Azure ML sur Azure Marketplace :

1.	Créez et publiez un service web RRS (Request-Response Service, service de requête-réponse) Azure ML.
2.	Depuis le portail de gestion Azure, déployez le service en production.
3.	Utilisez l'URL du service web à publier sur Azure Marketplace.
4.	Présentation du processus de publication : http://msdn.microsoft.com/library/azure/hh580725.aspx 
5.	Une fois soumise, votre offre est évaluée et doit être approuvée avant que vos clients puissent commencer à l'acheter. Le processus de publication peut prendre quelques jours ouvrés. Nous tentons de le réduire au maximum et nous proposerons une mise à jour dans nos prochaines communications.

## Consignes pour la publication sur Azure Marketplace

1.	Vous devez vous enregistrer en tant qu'éditeur. Pour plus d'informations, consultez la page suivante : <http://msdn.microsoft.com/library/azure/hh563872.aspx>
2.	Vous devez indiquer des informations sur votre offre, y compris un plan de tarification. Vous pouvez choisir de proposer un service payant ou gratuit. Pour plus d'informations, consultez la page suivante : <http://msdn.microsoft.com/library/azure/hh563873.aspx> 
3.	Pour être payé, vous devez indiquer des informations de paiement, notamment vos renseignements bancaires et fiscaux. Pour plus d'informations, consultez la page suivante : <http://msdn.microsoft.com/library/azure/hh563873.aspx>

## Options spécifiques à l'apprentissage automatique


1.	Lors de la création d'une offre, sélectionnez **Services de données**, puis cliquez sur **Créer un nouveau service de données**. 
 
	![Azure Marketplace][image1]

	<br />

2. Dans l'onglet **Service de données**, cliquez sur **Service web** pour la source de données.

	![Azure Marketplace][image2]

3.	Obtenez l'URL du service web et la clé d'API à partir du portail de gestion Azure :
	1.	Dans une fenêtre ou un onglet distinct du navigateur, connectez-vous au portail de gestion Azure ([https://manage.windowsazure.com](https://manage.windowsazure.com)). 
	2.	Sélectionnez **Machine Learning** dans le menu de gauche.
	3.	Cliquez sur **Services Web**, puis cliquez sur le service web que vous publiez.
	4.	Copiez la **clé d'API** vers un emplacement temporaire (par exemple, le bloc-notes).
	5.	Cliquez sur la **page d'aide de l'API** pour le type de service de requête/réponse.
	6.	Copiez l'**adresse du point de terminaison OData** à l'emplacement temporaire.

	<br />

3.	La boîte de dialogue de configuration du service de données Marketplace, collez l'adresse du point de terminaison OData dans **URL du service**.

	<br />

4. Pour l'authentification, choisissez **En-tête** comme **Schéma d'authentification**.

	- Saisissez " Autorisation " comme **Nom d'en-tête**.
	- Dans le champ **Valeur d'en-tête**, saisissez " Titulaire " (sans les guillemets), puis espace, puis collez la clé d'API.
	- Cochez la case **Ce service est OData**.
	- Cliquez sur **Tester la connexion** pour tester la connexion.

	<br />

5.	Sous Catégories :
	- Vérifiez que **Machine Learning** est sélectionné.



[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png

<!--HONumber=46--> 
 