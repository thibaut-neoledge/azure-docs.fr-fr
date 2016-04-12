<properties
	pageTitle="Ajouter l’API SharePoint Online à PowerApps Enterprise | Microsoft Azure"
	description="Créer ou configurer une API SharePoint Online dans l’environnement App Service de votre organisation"
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

# Créer une API SharePoint Online dans PowerApps Enterprise

Ajoutez l’API SharePoint Online à l’environnement de service d’application (locataire) de votre organisation.

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][14]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][15]

4. Dans **POWERAPPS**, sélectionnez **GÉRER LES API** : ![Accéder aux API inscrites][1]

5. Dans **Gérer les API**, sélectionnez **Ajouter** pour ajouter la nouvelle API : ![Add API][2]

6. Entrez un **Nom** descriptif pour votre API.
	
7. Dans **SOURCE**, sélectionnez **API DISPONIBLES** pour sélectionner les API prédéfinies, puis sélectionnez **SHAREPOINT ONLINE** : ![sélectionner l’API SharePoint Online][3]

8. Sélectionnez **PARAMÈTRES – CONFIGURER LES PARAMÈTRES REQUIS** : ![configurer les paramètres de l'API SharePoint Online][4]

9. Entrez l’*ID CLIENT* et la *CLÉ SECRÈTE DE L’APPLICATION* de votre application SharePoint Online Azure Active Directory (AAD). Si vous n’avez pas d’ID et de clé secrète client, consultez la section « Enregistrer une application AAD pour une utilisation avec PowerApps » dans cette rubrique pour savoir comment les créer.

	> [AZURE.IMPORTANT] Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.

10. Sélectionnez **OK** pour achever la procédure.

Quand vous avez terminé, une nouvelle API SharePoint Online est ajoutée à votre environnement App Service.


## Enregistrement d’une application AAD pour une utilisation avec l’API SharePoint Online PowerApps

1. Ouvrez le [portail Azure][5].

2. Sélectionnez **PARCOURIR**, puis **ACTIVE DIRECTORY** :

	> [AZURE.NOTE] Active Directory s’ouvre dans le portail Azure Classic.

3. Sélectionnez le nom de client de votre organisation : ![Démarrer Azure Active Directory][6]

4. Sélectionnez l’onglet **Applications**, puis sélectionnez **Ajouter** : ![Applications clientes AAD][7]

5. Dans **AJOUTER UNE APPLICATION** :

	a) Entrez un **nom** pour votre application. b) Pour le type d’application, conservez la valeur **Web**. c) Sélectionnez **Suivant**.


	![Ajouter une application AAD – Informations sur l’application][8]

6. Dans **Propriétés de l’application** :

	a) Entrez l’**URL de connexion** de votre application. Étant donné que vous allez vous authentifier dans AAD pour PowerApps, définissez l’URL de connexion sur \__https://login.windows.net_. b) Entrez un **URI ID d’application** valide pour votre application. c) Sélectionnez **OK**.

	![Ajouter une application AAD – Propriétés de l’application][9]

7. Une fois l’opération réussie, vous êtes redirigé vers la nouvelle application AAD. Sélectionnez **CONFIGURER** : ![Application AAD Contoso][10]

8. Définissez l’**URL DE RÉPONSE** située sous la section _OAUTH 2_ sur l’URL de redirection que vous avez reçue quand vous avez ajouté la nouvelle API SharePoint Online dans le portail Azure (dans cette rubrique). Sélectionnez **AJOUTER UNE APPLICATION** : ![Configurer l’application AAD Contoso][11]

9. Dans la fenêtre **AUTORISATIONS POUR D’AUTRES APPLICATIONS**, sélectionnez **OFFICE 365 EXCHANGE ONLINE**, puis sélectionnez **OK** : ![Délégué de l’application Contoso][12]

10. Dans la page Configurer, notez que _OFFICE 365 EXCHANGE ONLINE_ est ajouté à la liste _AUTORISATIONS POUR D’AUTRES APPLICATIONS_.

11. Sélectionnez **AUTORISATIONS DÉLÉGUÉES** pour _OFFICE 365 EXCHANGE ONLINE_, puis sélectionnez les autorisations suivantes :

	- Lire et écrire des éléments dans toutes les collections de sites
	- Lire et écrire des éléments et des listes dans toutes les collections de sites

	![Autorisations de délégué d’application Contoso][13]

Une nouvelle application Azure Active Directory est créée. Vous pouvez utiliser cette application dans la configuration de votre API SharePoint Online dans le portail Azure.

## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API SharePoint Online à PowersApps Enterprise. Ensuite, donnez accès à l’API pour les utilisateur afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribuer un accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-sharepointonline/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-sharepointonline/add-api.PNG
[3]: ./media/powerapps-create-api-sharepointonline/select-sharepointonline-api.PNG
[4]: ./media/powerapps-create-api-sharepointonline/configure-sharepointonline-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-sharepointonline/launch-aad.PNG
[7]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline.PNG
[13]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline-permissions.PNG
[14]: ./media/powerapps-create-api-sharepointonline/browseall.png
[15]: ./media/powerapps-create-api-sharepointonline/allresources.png

<!---------HONumber=AcomDC_0309_2016-->