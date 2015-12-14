<properties
	pageTitle="Ajouter l’API Recherche Bing à PowerApps Enterprise | Microsoft Azure"
	description="Créer ou configurer une API Recherche Bing dans l’environnement App Service Environment de votre organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="LinhTran"
	manager="gautamt"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Créer une API Recherche Bing dans l’environnement App Service Environment de votre organisation

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide d’une adresse *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][4]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][5]

4. Dans **PowerApps**, sélectionnez **Gérer les API** : ![Accès aux API inscrites][2]

2. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][3]

3. Tapez un **nom** descriptif pour votre API.

4. Dans **Source**, cliquez sur **API disponible** pour sélectionner les API prédéfinies, puis choisissez **recherche Bing** :

	a) Sélectionnez **Paramètres – Configurer les paramètres requis**.
	
	(b) Entrez *Clé de compte*. Si vous n’avez pas une clé de recherche Bing, créez gratuitement une [Offre de Recherche Bing][1] pour obtenir une clé.

	c) Sélectionnez **OK**.

5. Sélectionnez **OK** pour achever la procédure.

Lorsque vous avez terminé, une nouvelle API Recherche Bing est ajoutée à votre environnement App Service Environment.


## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Recherche Bing à PowersApps Enterprise. Ensuite, donnez accès à l’API aux utilisateurs afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://datamarket.azure.com/dataset/bing/search
[2]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[3]: ./media/powerapps-create-api-dropbox/add-api.PNG
[4]: ./media/powerapps-create-api-dropbox/browseall.png
[5]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_1203_2015-->