<properties
	pageTitle="Ajout de l’API Salesforce à PowerApps Enterprise | Microsoft Azure"
	description="Création ou configuration d’une API Salesforce dans l’App Service Environment de votre organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwerde"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/29/2016"
   ms.author="litran"/>

# Créer une API Salesforce dans PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/connectors-create-api-salesforce.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-salesforce.md)

Ajoutez l’API Salesforce à l’environnement de service d’application (locataire) de votre organisation.

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][14]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][15]

4. Dans **POWERAPPS**, sélectionnez **GÉRER LES API** : ![Accès aux API inscrites][1]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][2]

6. Tapez un **nom** descriptif pour votre API.
	
7. Dans **Source**, sélectionnez **API disponibles** pour sélectionner les API prédéfinies, puis sélectionnez **Salesforce** : ![sélectionner l’api salesforce][3]

8. Sélectionnez **Paramètres – Configurer les paramètres requis** : ![configurer les paramètres d’une api dropbox][7]

9. Entrez la *clé d’application* et la *question secrète de l’application* de votre application Salesforce. Si vous n’en avez pas, consultez la section « Enregistrer une application Salesforce pour une utilisation avec PowerApps » dans cette rubrique pour créer les valeurs Clé et Question secrète dont vous avez besoin.

	> [AZURE.IMPORTANT] Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.

10. Sélectionnez **OK** pour achever la procédure.

Quand vous avez terminé, une nouvelle API Salesforce est ajoutée à votre App Service Environment.


## Facultatif : Enregistrer une application Salesforce pour une utilisation avec PowerApps

Si vous ne disposez pas d’une application Salesforce avec sa clé et sa question secrète, procédez comme suit pour créer l’application et obtenir les valeurs dont vous avez besoin.

1. Ouvrez la [page d’accueil des développeurs Salesforce][5], et connectez-vous avec votre compte Salesforce. 

2. Sur la page d’accueil, sélectionnez votre profil et sélectionnez **Setup** : ![Page d’accueil Salesforce][6]

3. Sélectionnez **Create**, puis sélectionnez **Apps**. Dans la page **Apps**, sélectionnez **New** sous **Connected Apps** : ![Création d’applications Salesforce][7]

4. Dans **New Connected App** :

	1. Renseignez le champ **Connected App Name**.  
	2. Renseignez le champ **API Name**.  
	3. Renseignez le champ **Contact Email**.  
	4. Sous _API (Enable OAuth Settings)_, sélectionnez **Enable OAuth Settings**, puis définissez **Callback URL** sur l’URL que vous avez reçue lorsque vous avez ajouté la nouvelle API Salesforce dans le portail Azure (dans cette rubrique).  

5. Dans la section _Selected OAuth scopes_, ajoutez les étendues suivantes à **Selected OAuth Scopes** :

	- Access and manage your Chatter data (chatter\_api)
	- Access and manage your data (api)
	- Allow access to your unique identifier (openid)
	- Perform requests on your behalf at any time (refresh\_token, offline\_access)

6. Cliquez sur **Save** pour enregistrer vos modifications : ![Nouvelle application Salesforce][8]

Une application Salesforce est créée. Vous pouvez utiliser cette application dans la configuration de votre API Salesforce dans le portail Azure.

## Consulter les API REST

Informations de référence sur l’[API REST Salesforce](../connectors/connectors-create-api-salesforce.md).

## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Salesforce à PowerApps Enterprise. Maintenant, donnez accès à l’API aux utilisateurs, afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-salesforce/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-salesforce/add-api.PNG
[3]: ./media/powerapps-create-api-salesforce/select-salesforce-api.PNG
[4]: ./media/powerapps-create-api-salesforce/configure-salesforce-api.PNG
[5]: https://developer.salesforce.com
[6]: ./media/powerapps-create-api-salesforce/salesforce-developer-homepage.PNG
[7]: ./media/powerapps-create-api-salesforce/salesforce-create-app.PNG
[8]: ./media/powerapps-create-api-salesforce/salesforce-new-app.PNG
[14]: ./media/powerapps-create-api-salesforce/browseall.png
[15]: ./media/powerapps-create-api-salesforce/allresources.png

<!---HONumber=AcomDC_0330_2016-->