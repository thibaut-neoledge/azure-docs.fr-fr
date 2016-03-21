<properties
	pageTitle="Ajout de l’API Twitter à PowerApps Enterprise | Microsoft Azure"
	description="Création ou configuration d’une API Twitter dans l’environnement App Service de votre organisation"
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
   ms.date="03/03/2016"
   ms.author="litran"/>

# Créer une API Twitter dans PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-twitter.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-twitter.md)

Ajoutez l’API Twitter à l’environnement App Service (locataire) de votre organisation.

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société. 

2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][14]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][15]

4. Dans **POWERAPPS**, sélectionnez **GÉRER LES API** : ![Accéder aux API inscrites][1]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][2]

6. Entrez un **Nom** descriptif pour votre API.
	
7. Dans **SOURCE**, sélectionnez **API DISPONIBLES** pour sélectionner les API prédéfinies, puis sélectionnez **Twitter** : ![sélectionner l'API Twitter][3]

8. Sélectionnez **PARAMÈTRES – CONFIGURER LES PARAMÈTRES REQUIS** : ![configurer les paramètres de l'API Twitter][4]

9. Entrez la *Clé du client* et la *Question secrète du client* de votre application Twitter. Si vous n’en avez pas, consultez la section « Enregistrer une application Twitter pour une utilisation avec PowerApps » dans cette rubrique pour créer des valeurs clé et question secrète dont vous avez besoin.

	> [AZURE.IMPORTANT] Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.

10. Sélectionnez **OK** pour achever la procédure.

Quand vous avez terminé, une nouvelle API Twitter est ajoutée à votre App Service Environment.


## Facultatif : Inscription d’une application Twitter pour une utilisation avec PowerApps

Si vous ne disposez pas d’une application Twitter avec sa clé et sa clé secrète, procédez comme suit pour créer l'application et obtenir les valeurs dont vous avez besoin.

1. Accédez à [https://apps.twitter.com/](https://apps.twitter.com) et connectez-vous avec votre compte Twitter.

2. Sélectionnez **Créer une application** : ![Page des applications Twitter][6]

3. Dans **Créer une application** :
   
	1. Entrez une valeur dans le champ **Nom**.  
	2. Entrez une valeur dans le champ **Description**.  
	3. Entrez une valeur dans le champ **Site Web**.  
	4. Définissez la valeur **URL de rappel** sur l’URL que vous avez reçue quand vous avez ajouté la nouvelle API Twitter dans le portail Azure (dans cette rubrique).  
	5. Acceptez le contrat de développeur, et cliquez sur **Créer votre application Twitter**.  

	![Créer une application Twitter][7]

4. Une fois l’application créée, vous êtes redirigé vers la page de l’application.

Une application Twitter est créée. Vous pouvez utiliser cette application dans la configuration de votre API Twitter dans le portail Azure.

## Consulter les API REST

Informations de référence sur l'[API REST Twitter](../connectors/create-api-twitter.md)


## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Twitter à PowersApps Enterprise. Ensuite, donnez accès à l’API pour les utilisateur afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)


<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_0309_2016-->