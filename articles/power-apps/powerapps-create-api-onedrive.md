<properties
	pageTitle="Ajouter l’API OneDrive à PowerApps Enterprise | Microsoft Azure"
	description="Création ou configuration d’une nouvelle API OneDrive dans l’environnement App Service de votre organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Création d’une nouvelle API OneDrive dans l’environnement App Service de votre organisation

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Sélectionnez **Parcourir** dans la barre des tâches : ![][14]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][15]

4. Dans **PowerApps**, sélectionnez **Gérer les API** : ![Accédez aux API inscrites][1]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][2]

6. Entrez un **nom** descriptif pour votre API.
	
7. Dans **Source**, sélectionnez **API disponibles** pour sélectionner les API prédéfinies, puis sélectionnez **OneDrive** : ![Sélectionnez API OneDrive][3]

8. Sélectionnez **Paramètres – Configurer les paramètres requis** : ![configurez les paramètres de l’API OneDrive][4]

9. Entrez la *clé d’application* et la *question secrète de l’application* de votre application OneDrive. Si vous n’en avez pas, consultez la section « Enregistrer une application OneDrive pour une utilisation avec PowerApps » dans cette rubrique pour créer les valeurs clé et question secrète dont vous avez besoin.

	> [AZURE.IMPORTANT]Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.

10. Sélectionnez **OK** pour achever la procédure.

Quand vous avez terminé, une nouvelle API OneDrive est ajoutée à votre environnement App Service.

## Facultatif : Enregistrement d’une application OneDrive pour une utilisation avec PowerApps

Si vous ne disposez pas d’une application OneDrive avec les valeurs clé et question secrète, alors procédez comme suit pour créer l’application et obtenir les valeurs dont vous avez besoin.

1. Accédez à la [page de création d’application][5] dans _Centre de développement de compte Microsoft_ et connectez-vous avec votre _compte Microsoft_.

2. Entrez le **nom de votre Application**, puis sélectionnez **J’accepte**: ![Nouvelle application OneDrive][6]

3. Sur la page Paramètres :

	a) Sélectionnez **Paramètres API**. b) Paramétrez l’URL de redirection avec celle que vous avez reçue lorsque vous avez ajouté la nouvelle API OneDrive API dans le portail Azure Portal (dans cette rubrique). c) Sélectionnez **Enregistrer**.

	![Paramètres de l’application API OneDrive][7]

Une nouvelle application OneDrive est créée. Vous pouvez utiliser cette application dans la configuration de votre API OneDrive dans le portail Azure.

## Résumé et étapes suivantes
Dans cette rubrique, vous avez ajouté l’API OneDrive à PowersApps Enterprise. Ensuite, donnez accès à l’API aux utilisateurs afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et ouvrir l’accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG
[5]: https://account.live.com/developers/applications/create
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG
[14]: ./media/powerapps-create-api-onedrive/browseall.png
[15]: ./media/powerapps-create-api-onedrive/allresources.png

<!---HONumber=AcomDC_1203_2015-->