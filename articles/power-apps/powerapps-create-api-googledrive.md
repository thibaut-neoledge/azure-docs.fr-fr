<properties
	pageTitle="Ajouter l’API Google Drive à PowerApps Enterprise | Microsoft Azure"
	description="Créer ou configurer une API Google Drive dans l’environnement App Service de votre organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Créer une API Google Drive dans PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-googledrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-googledrive.md)

Ajoutez l’API Google Drive à l’environnement de service d’application (locataire) de votre organisation.

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][15]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][16]

4. Dans **POWERAPPS**, sélectionnez **GÉRER LES API** : ![Accéder aux API inscrites][1]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][2]

6. Entrez un **Nom** descriptif pour votre API.
	
7. Dans **SOURCE**, sélectionnez **API DISPONIBLES** pour sélectionner les API prédéfinies, puis sélectionnez **GOOGLE DRIVE** : ![sélectionnez l’API Google Drive][3]

8. Sélectionnez **PARAMÈTRES – CONFIGURER LES PARAMÈTRES REQUIS** : ![configurer les paramètres de l’API Google Drive][4]

9. Entrez la *clé d’application* et la *question secrète de l’application* de votre application Google Drive. Si vous n’en avez pas, consultez la section « Enregistrer une application Google Drive pour une utilisation avec PowerApps » dans cette rubrique pour créer des valeurs clé et question secrète dont vous avez besoin.

	> [AZURE.IMPORTANT] Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.

10. Sélectionnez **OK** pour achever la procédure.

Quand vous avez terminé, une nouvelle API Google Drive est ajoutée à votre environnement App Service.


## Facultatif : Enregistrement d’une application Google Drive pour une utilisation avec PowerApps

Si vous ne disposez pas d’une application Google Drive avec les valeurs clé et question secrète, alors procédez comme suit pour créer l’application et obtenir les valeurs dont vous avez besoin.

1. Connectez-vous à la [Console développeur de Google][5] \: ![Console développeur de Google][6]

2. Sélectionnez **CRÉER UN PROJET**.

3. Entrez le nom de votre application, acceptez les termes et conditions, puis sélectionnez **CREATE** : ![créer un projet Google Drive][7]

4. Une fois le projet créé, sélectionnez **UTILISER LES API GOOGLE** : ![Utiliser les API Google][8]

5. Dans la vue d’ensemble, sélectionnez **DRIVE API** : ![Présentation de l'API Google Drive][9]

6. Sélectionnez **ACTIVER L’API** : ![Activer l’API Google Drive][10]

7. Une fois Drive API activé, sélectionnez **IDENTIFIANTS**, puis **ID CLIENT OAUTH 2.0** : ![Ajouter des identifiants][12]

8. Sélectionnez **CONFIGURER L’ÉCRAN D’AUTHORISATION**.

9. Dans l’onglet **ÉCRAN D’AUTORISATION OAUTH**, entrez un **nom de produit**, puis sélectionnez **ENREGISTRER**: ![Configurer l’écran d’autorisation][13]

10. Dans la page Créer un ID client :

	1. Sous **Type d’application**, sélectionnez **Application Web**.  
	2.  Entrez un nom pour le client.  
	3. Définissez l’URL de redirection sur l’URL que vous avez reçue quand vous avez ajouté la nouvelle API Google Drive dans le portail Azure (dans cette rubrique).  
	4. Sélectionnez **Créer**.  

	![Créer un identifiant client][14]

11. L’ID client et le code secret du client de l'application inscrite apparaissent.

Une nouvelle application Google Drive est créée. Vous pouvez utiliser cette application dans la configuration de votre API Google Drive dans le portail Azure.

## Consulter les API REST

Informations de référence sur l’[API REST Google Drive](../connectors/connectors-create-api-googledrive.md).

## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Google Drive à PowersApps Enterprise. Ensuite, donnez accès à l’API pour les utilisateur afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

<!---HONumber=AcomDC_0330_2016-->