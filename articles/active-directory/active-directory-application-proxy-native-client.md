<properties
	pageTitle="Activation d'applications clientes natives de manière à ce qu'elles interagissent avec des applications proxy | Microsoft Azure"
	description="Explique comment activer des applications clientes natives de manière à communiquer avec le connecteur de proxy d'application Azure AD pour fournir un accès à distance sécurisé à vos applications locales."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="kgremban"/>

# Activation d'applications clientes natives de manière à ce qu'elles interagissent avec des applications proxy
Le proxy d'application Azure Active Directory est largement utilisé pour publier des applications de navigateur telles que SharePoint, Outlook Web Access et des applications métier personnalisées. Il peut également être utilisé pour publier des applications back-end HTTP consommées à l'aide de clients natifs. Pour cela, la prise en charge de jetons générés par Azure AD qui sont envoyés dans des en-têtes d'autorisation HTTP standard est nécessaire.


![](./media/active-directory-application-proxy-native-client/richclientflow.png)


La méthode recommandée pour publier de telles applications consiste à utiliser la bibliothèque d'authentification Azure AD qui prend en charge toutes les contraintes liées à l'authentification pour de nombreux environnements client différents. Le proxy d'application est conforme au [scénario Application native vers API Web](active-directory-authentication-scenarios.md#native-application-to-web-api). Pour ce faire, procédez comme suit :

1. Publiez votre application proxy comme vous le feriez pour toute autre application, affectez des utilisateurs et affectez-leur des licences premium ou de base. Pour plus d'informations, consultez [Publier des applications avec le proxy d'application](active-directory-application-proxy-publish.md).
2. Configurez votre application native comme suit :
  3. Connectez-vous au portail de gestion Azure.
  4. Cliquez sur l'icône d'Active Directory dans le menu de gauche, puis cliquez sur le répertoire souhaité.
  5. Dans le menu supérieur, cliquez sur Applications. Si aucune application n’a été ajoutée à votre répertoire, cette page affiche uniquement le lien Ajouter une application. Cliquez sur le lien. Vous pouvez également cliquer sur le bouton Ajouter dans la barre de commandes.
  4. Sur la page **Que voulez-vous faire**, cliquez sur le lien vers **Ajouter une application développée par mon organisation**.
  5. Sur la page **Parlez-nous de votre application**, spécifiez un nom pour votre application et choisissez **Application cliente native**, qui représente une application installée sur un périphérique tel qu'un téléphone ou un ordinateur. Une fois l'opération terminée, cliquez sur la flèche dans le coin inférieur droit de la page.
  6. Sur la page des **propriétés de l'application**, indiquez l'**URI de redirection**, puis cliquez sur la case à cocher dans le coin inférieur droit de la page. </br>Votre application a été ajoutée, et vous allez être redirigé vers la page de démarrage rapide pour votre application.
8. Activez l'application native à exposer à d'autres applications dans votre répertoire :
  9. Dans le menu principal, cliquez sur **Applications**, sélectionnez la nouvelle application native, puis cliquez sur **configurer**.
  10. Faites défiler la page jusqu'à la section **Autorisations pour d'autres applications**. Cliquez sur le bouton **Ajouter une application**, puis sélectionnez l'application de proxy à laquelle vous souhaitez accorder l'accès à l'application native et cochez la case dans le coin inférieur droit. Dans la liste déroulante **Autorisations déléguées**, sélectionnez la nouvelle autorisation. </br>

![](./media/active-directory-application-proxy-native-client/delegate_native_app.png) </br></br> 4. Modifiez le code de l'application native dans le contexte de l'authentification de l'Active Directory Authentication Library (ADAL) de manière à inclure les éléments suivants :

		// Acquire Access Token from AAD for Proxy Application
		AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
		AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

		//Use the Access Token to access the Proxy Application
		HttpClient httpClient = new HttpClient();
		httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
		HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Les variables doivent être remplacées comme suit :


- Le **TenantId** se trouve dans le GUID dans l'URL de la page de **configuration** de l'application, juste après « /Répertoire/ ».
- L'**URL de front-end** est l'URL de front-end entré dans l'application de Proxy et de se trouve sur la page de **Configuration** de l'application proxy.
- L'**ID client** de l'application native est indiqué sur la page de **configuration** page de l'application native.
- L'**URI de redirection de l'application native** est indiqué sur la page de **configuration** de l'application native.

![](./media/active-directory-application-proxy-native-client/new_native_app.png) </br> </br>Pour plus d'informations sur le flux d'application native, consultez [Application native vers API Web](active-directory-authentication-scenarios.md#native-application-to-web-api).






## Et ensuite ?
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :


- [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)


### En savoir plus sur le Proxy d’application
- [Consultez notre aide en ligne](active-directory-application-proxy-enable.md)
- [Consultez le blog sur le Proxy d’application](http://blogs.technet.com/b/applicationproxyblog/)
- [Regardez nos vidéos sur Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Ressources supplémentaires
* [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
* [Identité Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_1125_2015-->