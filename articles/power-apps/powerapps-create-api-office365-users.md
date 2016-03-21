<properties
	pageTitle="Ajouter l’API Office 365 Utilisateurs à PowerApps Enterprise | Microsoft Azure"
	description="Créer ou configurer une API Office 365 Utilisateurs dans l’environnement App Service de votre organisation"
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
   ms.date="03/03/2016"
   ms.author="litran"/>

# Créer une API Office 365 Users dans PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

Ajoutez l’API Office 365 Users à l’environnement de service d’application (locataire) de votre organisation.

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][14]

3. Faites défiler la liste pour sélectionner PowerApps ou tapez *powerapps* : ![][15]

4. Dans **PowerApps**, sélectionnez **Gérer les API** : ![Rechercher des API inscrites][1]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][2]

6. Entrez un **nom** descriptif pour votre API.
	
7. Dans **Source**, sélectionnez **API disponibles** pour sélectionner les API prédéfinies, puis sélectionnez **Office 365 Utilisateurs** : ![Sélectionner l’API Office 365 Utilisateurs][3]

8. Sélectionnez **Paramètres – Configurer les paramètres requis** : ![Configurer les paramètres de l’API Office 365 Utilisateurs][4]

9. Entrez l’*ID client* et la *clé secrète client* de votre application Office 365 Azure Active Directory (AAD). Si vous n’avez pas d’ID ni de clé secrète client, consultez la section « Enregistrer une application AAD pour une utilisation avec PowerApps » dans cette rubrique pour savoir comment les créer.

	> [AZURE.IMPORTANT] Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.

10. Sélectionnez **OK** pour terminer les étapes.

Quand vous avez terminé, une nouvelle API Office 365 Utilisateurs est ajoutée à votre environnement App Service.

## Facultatif : Enregistrer une application AAD pour une utilisation avec l’API Office 365 Utilisateurs PowerApps

Si vous ne disposez pas d’une application AAD avec sa clé et sa clé secrète, procédez comme suit pour créer l’application et obtenir les valeurs dont vous avez besoin.

1. Ouvrez le [portail Azure][5].

2. Sélectionnez **Parcourir**, puis **Active Directory** :

	> [AZURE.NOTE] Active Directory s’ouvre dans le portail Azure Classic.

3. Sélectionnez le nom de client de votre organisation : ![Démarrer Azure Active Directory][6]

4. Sélectionnez l’onglet **Applications**, puis sélectionnez **Ajouter** : ![Applications clientes AAD][7]

5. Dans **AJOUTER UNE APPLICATION** :

	1. Entrez un **Nom** pour votre application.  
	2. Laissez le type d’application sur **Web**.  
	3. Sélectionnez **Suivant**.  

	![Ajouter une application AAD – Informations sur l’application][8]

6. Dans **Propriétés de l’application** :

	1. Entrez l’**URL de connexion** de votre application. Étant donné que vous allez vous authentifier dans AAD pour PowerApps, définissez l’URL de connexion sur \__https://login.windows.net_.
2. Entrez un **URI ID d’application** valide pour votre application.  
	3. Sélectionnez **OK**.  

	![Ajouter une application AAD – Propriétés de l’application][9]

7. Une fois l’opération réussie, vous êtes redirigé vers la nouvelle application AAD. Sélectionnez **Configurer** : ![Application AAD Contoso][10]

8. Définissez l’**URL de réponse** située sous la section _OAuth 2_ sur l’URL de redirection que vous avez reçue quand vous avez ajouté la nouvelle API Office 365 Utilisateurs dans le portail Azure (dans cette rubrique). Sélectionnez **Ajouter une application** : ![Configuration de l’application AAD Contoso][11]

9. Dans la fenêtre **Autorisations pour d’autres applications**, sélectionnez **API Office 365 Unifié (version préliminaire) **, puis sélectionnez **OK**.

10. Dans la page Configurer, notez que _API Office 365 Unifié (version préliminaire)_ est ajouté à la liste _Autorisations pour d’autres applications_.

11. Pour _API Office 365 Unifié (version préliminaire)_, sélectionnez **Autorisations déléguées**, puis sélectionnez l’autorisation **Lire les profils de base de tous les utilisateurs**.

Une nouvelle application Azure Active Directory est créée. Vous pouvez utiliser cette application dans la configuration de votre API Office 365 Utilisateurs dans le portail Azure.

Pour obtenir des informations intéressantes sur les applications AAD, consultez [Comment et pourquoi les applications sont ajoutées à Azure AD](../active-directory/active-directory-how-applications-are-added.md).

## Consulter les API REST

Informations de référence sur l’[API REST Office 365 Users](../connectors/create-api-office365-users.md).

## Résumé et étapes suivantes
Dans cette rubrique, vous avez vu comment ajouter l’API Office 365 Users à PowersApps Enterprise. Ensuite, donnez accès à l’API pour les utilisateur afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-office365-users/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-users/add-api.PNG
[3]: ./media/powerapps-create-api-office365-users/select-office365-users-api.PNG
[4]: ./media/powerapps-create-api-office365-users/configure-office365-users-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-users/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-users/contoso-aad-app-configure.PNG

<!------HONumber=AcomDC_0309_2016-->