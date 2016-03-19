<properties
	pageTitle="Ajouter l’API SharePoint Server à PowerApps Enterprise | Microsoft Azure"
	description="Créer ou configurer une API SharePoint Server dans l’environnement App Service de votre organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# Créer une API SharePoint Server dans l’environnement App Service de votre organisation

## Créer l’API dans le portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), connectez-vous avec votre compte professionnel. Par exemple, connectez-vous à l’aide *VotreNomUtilisateur*@*VotreSociété*.com. Lorsque vous procédez ainsi, vous êtes automatiquement connecté à l’abonnement de votre société.
 
2. Dans la barre des tâches, sélectionnez **Parcourir** : ![][14]

3. Dans la liste, vous pouvez faire défiler pour sélectionner PowerApps ou taper *powerapps* : ![][15]

4. Dans **POWERAPPS**, sélectionnez **GÉRER LES API** : ![Accéder aux API inscrites][5]

5. Dans **GÉRER LES API**, sélectionnez **AJOUTER** pour ajouter la nouvelle API : ![Add API][6]

6. Entrez un **Nom** descriptif pour votre API.
7. Dans **SOURCE**, sélectionnez **API DISPONIBLES** pour sélectionner les API prédéfinies, puis sélectionnez **SHAREPOINT SERVER**. 
8. Sélectionnez **PARAMÈTRES – CONFIGURER LES PARAMÈTRES REQUIS**.
9. Entrez l’*ID CLIENT* et la *CLÉ D’APPLICATION* du serveur SharePoint Azure Active Directory (application AAD) ainsi que l’*URL SHAREPOINT* et l’*ID DE RESSOURCE* de l’application Proxy AAD. Suivez les étapes décrites dans la section suivante pour configurer la connectivité à votre serveur SharePoint local.  

	> [AZURE.IMPORTANT]Enregistrez l’**URL de redirection**. Vous pourrez avoir besoin de cette valeur plus loin dans cette rubrique.
	
10. Sélectionnez **OK** pour achever la procédure.

Quand vous avez terminé, une nouvelle API SharePoint Server est ajoutée à votre environnement App Service.


## Configurer la connectivité à un serveur SharePoint local

Le serveur SharePoint utilise Active Directory pour l'authentification utilisateur. Les API dans les environnements App Service sont authentifiées à l'aide d'Azure Active Directory (AAD). Vous n'avez pas besoin d’échanger le jeton AAD de l’utilisateur et de le convertir en jeton AD. Ce jeton AD peut ensuite être utilisé pour se connecter au service local.

[Proxy d'application Azure (proxy AAD)](../active-directory-application-proxy-publish.md) est utilisée pour cette spécification. Il s’agit d’un service Azure dans GA qui sécurise l'accès à distance et l'authentification unique des applications web locales. La procédure d'activation du proxy AAD est bien documentée dans MSDN. À un niveau supérieur, les étapes sont les suivantes :

1. [Activer les services de proxy d’application](../active-directory-application-proxy-enable.md) – Cela inclut :  

	- Activer le proxy d’application dans Azure AD
	- Installer et inscrire le connecteur du proxy d'application Azure AD

2. [Publier des applications avec le proxy d’application](../active-directory-application-proxy-publish.md) – Cela inclut :

	- Publiez une application Proxy d'application à l’aide de l’assistant. Notez l'URL du site sharepoint intranet externe une fois l'application Proxy créée.
	- Affecter des utilisateurs et un groupe à l’application
	- Entrez la configuration avancée comme le SPN (nom principal de service) utilise par le connecteur du proxy d'application afin d’extraire le jeton Kerberos local.

Une fois l'application Proxy créée, vous devez créer une autre application AAD qui délègue l’accès à l'application de proxy. Cela est nécessaire pour obtenir le jeton d'accès et actualiser le jeton requis pour le flux d’autorisation. Vous pouvez créer une application AAD en suivant [ces instructions](../active-directory-integrating-applications.md).

## Résumé et étapes suivantes
Vous venez de voir comment ajouter l’API Office 365 Outlook à PowersApps Enterprise. Ensuite, donnez accès à l’API pour les utilisateur afin qu’ils puissent l’ajouter à leurs applications :

[Ajouter une connexion et attribution d’accès aux utilisateurs](powerapps-manage-api-connection-user-access.md)


<!--References-->
[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_1203_2015-->