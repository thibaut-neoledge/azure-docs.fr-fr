<properties
	pageTitle="Ajouter l’API Dropbox à PowerApps Enterprise | Microsoft Azure"
	description="Créer ou configurer une API Dropbox dans l’environnement App Service Environment de votre organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="litran"/>

# Créer une API Dropbox dans PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

Ajoutez l’API Dropbox à l’environnement App Service (locataire) de votre organisation.

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** :  
![][12]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* :  
![][13]

4. Dans **POWERAPPS**, sélectionnez **GÉRER LES API** :  
![Accès aux API inscrites][4]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API :  
![Add API][5]

6. Tapez un **nom** descriptif pour votre API.
	
7. Dans **Source**, sélectionnez **API disponibles** pour voir les API prédéfinies, puis sélectionnez **Dropbox** :  
![sélection d’une api dropbox][6]

8. Sélectionnez **Paramètres – Configurer les paramètres requis** :  
![configuration des paramètres d’une API dropbox][7]

9. Entrez les valeurs **Clé d’application** et **Question secrète de l’application** de votre application Dropbox. Si vous n’avez pas de clé et de question secrète, consultez la section « Inscription d’une application Dropbox pour une utilisation avec PowerApps » dans cette rubrique pour créer les valeurs dont vous avez besoin.

	> [AZURE.IMPORTANT] Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.

10. Sélectionnez **OK** pour achever la procédure.


Quand vous avez terminé, une nouvelle API Dropbox est ajoutée à votre environnement App Service Environment.


## Facultatif : Inscription d’une application Dropbox pour une utilisation avec PowerApps

Si vous ne disposez pas d’une application Dropbox avec sa clé et sa clé secrète, procédez comme suit pour créer l’application et obtenir les valeurs dont vous avez besoin.

1. Accédez à [Dropbox][1] et connectez-vous avec votre compte.

2. Accédez au site de développement Dropbox et sélectionnez **My Apps** (Mes applications) :  
![Site de développement Dropbox][8]

3. Sélectionnez **Create app** (Créer une application) :  
![Création d’application Dropbox][9]

4. Dans **Create a new app on the Dropbox platform** (Créer une application sur la plateforme Dropbox) :

	1. Dans **Choose API**, sélectionnez **Dropbox API**.  
	2. Dans **Choose the type of access you need**, sélectionnez **Full Dropbox...**.  
	3. Entrez le nom de votre application.  

	![Création d’application Dropbox page 1][10]

5. Dans la page des paramètres de l’application :

	1. Dans **OAuth 2**, définissez la valeur **Redirect URL** (URL de redirection) sur l’URL que vous avez reçue quand vous avez ajouté la nouvelle API Dropbox dans le portail Azure (dans cette rubrique). Sélectionnez **Ajouter**.  
	2. Sélectionnez le lien **Show** pour révéler la **question secrète de l’application** :  

	![Création d’application Dropbox page 2][11]

Une application Dropbox est créée. Vous pouvez utiliser cette application dans la configuration de votre API Dropbox dans le portail Azure.

## Consulter les API REST

Informations de référence sur l’[API REST Dropbox](../connectors/create-api-dropbox.md).


## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Dropbox à PowersApps Enterprise. Ensuite, donnez accès à l’API aux utilisateurs afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[4]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[5]: ./media/powerapps-create-api-dropbox/add-api.PNG
[6]: ./media/powerapps-create-api-dropbox/select-dropbox-api.PNG
[7]: ./media/powerapps-create-api-dropbox/configure-dropbox-api.PNG
[8]: ./media/powerapps-create-api-dropbox/dropbox-developer-site.PNG
[9]: ./media/powerapps-create-api-dropbox/dropbox-create-app.PNG
[10]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page1.PNG
[11]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page2.PNG


[12]: ./media/powerapps-create-api-dropbox/browseall.png
[13]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_0309_2016-->