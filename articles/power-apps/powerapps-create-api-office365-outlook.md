<properties
	pageTitle="Ajout de l’API Office 365 Outlook à PowerApps Enterprise | Microsoft Azure"
	description="Création ou configuration d’une API Office 365 Outlook dans l’environnement de service d’application de votre organisation"
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

# Créer une API Office 365 Outlook dans PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-office365-outlook.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-outlook.md)

Ajoutez l’API Office 365 Outlook à l’environnement de service d’application (locataire) de votre organisation.

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][14]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou saisir *powerapps* : ![][15]

4. **Services PowerApps Services**, sélectionnez **Gérer les API** : ![Rechercher des API inscrites][1]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][2]

6. Saisissez un **nom** descriptif pour votre API.
	
7. Dans **Source**, sélectionnez **API disponibles** pour sélectionner les API prédéfinies, puis sélectionnez **Office 365 Outlook** : ![sélectionner l’api Office 365 Outlook][3]

8. Sélectionnez **Paramètres – Configurer les paramètres requis** : ![configurer les paramètres de l’API Office 365 Outlook][4]

9. Saisissez les valeurs *Clé d’application* et *Question secrète* de votre application Office 365 Azure Active Directory (AAD). Si vous n’en avez pas, consultez la section « Enregistrer une application AAD pour une utilisation avec PowerApps » dans cette rubrique pour créer des valeurs clé et question secrète dont vous avez besoin.
 
	> [AZURE.IMPORTANT] Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.

10. Sélectionnez **OK** pour terminer les étapes.

Lorsque vous avez terminé, une nouvelle API Office 365 Outlook est ajoutée à votre environnement App Service.


## Facultatif : Enregistrement d’une application AAD pour une utilisation avec l’API Office 365 PowerApps

Si vous ne disposez pas d’une application AAD avec les valeurs clé et question secrète, alors procédez comme suit pour créer l’application et obtenir les valeurs dont vous avez besoin.

1. Ouvrez le [portail Azure][5].

2. Sélectionnez **Parcourir** puis sélectionnez **Active Directory**.

	>[AZURE.NOTE] Cela ouvre Active Directory dans le portail Azure Classic.

3. Sélectionnez le nom de client de votre organisation : ![Démarrer d’Azure Active Directory][6]

4. Sélectionnez l’onglet **Applications**, puis sélectionnez **Ajouter** : ![Applications clientes AAD][7]

5. Dans **AJOUTER UNE APPLICATION** :

	1. Entrez un **Nom** pour votre application.  
	3. Laissez le type d’application sur **Web**.  
	3. Sélectionnez **Suivant**.  

	![Ajouter une application AAD – Informations sur l’application][8]

6. Dans **Propriétés de l’application** :

	1. Entrez l’**URL de connexion** de votre application. Étant donné que vous allez vous authentifier dans AAD pour PowerApps, définissez l’URL de connexion sur \__https://login.windows.net_.
2. Entrez un **URI ID d’application** valide pour votre application.  
	3. Sélectionnez **OK**.  

	![Ajouter une application AAD – Propriétés de l’application][9]

7. Une fois l’opération réussie, vous êtes redirigé vers la nouvelle application AAD. Sélectionnez **Configurer** : ![Application AAD Contoso][10]

8. Définissez l’**URL de réponse** sous la section _OAuth 2_ de l’URL de redirection que vous avez reçue lorsque vous avez ajouté la nouvelle API de Office 365 Outlook dans le portail Azure (dans cette rubrique). Sélectionnez ensuite **Ajouter une application** : ![Configurer l’application AAD Contoso][11]

9. Dans la fenêtre **Autorisations pour d’autres applications**, sélectionnez **Office 365 Exchange Online**, puis sélectionnez **OK** : ![Délégué de l’application Contoso][12]

10. Dans la page Configurer, notez que _OFFICE 365 EXCHANGE ONLINE_ est ajouté à la liste _AUTORISATIONS POUR D’AUTRES APPLICATIONS_.

11. Sélectionnez **Autorisations déléguées** pour _Office 365 Exchange Online_, puis sélectionnez les autorisations suivantes :

	- Lecture et écriture des contacts de l’utilisateur
	- Lecture des contacts de l’utilisateur
	- Lecture et écriture des calendriers de l’utilisateur
	- Lecture des calendriers des utilisateurs
	- Envoi de messages en tant qu’utilisateur
	- Lecture et écriture du courrier de l’utilisateur
	- Lecture du courrier de l’utilisateur

	![Autorisations de délégué d’application Contoso][13]

Une nouvelle application Azure Active Directory a été créée. Vous pouvez utiliser cette application dans votre configuration de l’API Office 365 Outlook dans le portail Azure.

Pour obtenir des informations intéressantes sur les applications AAD, consultez [Comment et pourquoi les applications sont ajoutées à Azure AD](../active-directory/active-directory-how-applications-are-added.md).

## Consulter les API REST

Informations de référence sur l’[API REST Office 365 Outlook](../connectors/create-api-office365-outlook.md).


## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Office 365 Outlook à PowersApps Enterprise. Ensuite, donnez accès à l’API pour les utilisateur afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-office365-outlook/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-outlook/add-api.PNG
[3]: ./media/powerapps-create-api-office365-outlook/select-office365-outlook-api.PNG
[4]: ./media/powerapps-create-api-office365-outlook/configure-office365-outlook-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-outlook/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.PNG
[13]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_0309_2016-->