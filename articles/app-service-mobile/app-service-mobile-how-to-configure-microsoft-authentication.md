<properties
	pageTitle="Comment configurer l'authentification par compte Microsoft pour votre application App Services"
	description="Découvrez comment configurer l'authentification par compte Microsoft pour votre application App Services."
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="10/30/2015"
	ms.author="mahender"/>

# Comment configurer votre application App Service pour utiliser une connexion par compte Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser un compte Microsoft comme fournisseur d’authentification.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Inscrivez votre application avec un compte Microsoft

1. Connectez-vous au [portail de gestion Azure] et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application de compte Microsoft.

2. Accédez à la page [Mes applications] dans le Centre des développeurs de compte Microsoft, puis connectez-vous avec votre compte Microsoft si nécessaire.

4. Cliquez sur **Créer une application**, puis tapez le **Nom de l'application** et cliquez sur **J'accepte**.

5. Cliquez sur **Paramètres de l’API**. Sélectionnez **Oui** pour **Application cliente mobile ou de bureau**. Dans le champ **URL de redirection** saisissez l’**URL de redirection** de votre application, puis cliquez sur **Enregistrer**. Votre URI de redirection correspond à l’URL de votre application suivie du chemin d’accès, _/.auth/login/microsoftaccount/callback_. Par exemple : `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Assurez-vous d'utiliser le schéma HTTPS.

	![][0]


	> [AZURE.NOTE]Si vous utilisez la passerelle App Service au lieu de la fonction d’authentification/autorisation d’App Service, votre URL de redirection utilise à la place l’URL de la passerelle avec le chemin d’accès _/signin-microsoft_.


6. Cliquez sur **Paramètres d'application** et notez les valeurs de l'**ID du client** et de la **Clé secrète client**.


    > [AZURE.NOTE]La clé secrète client est une information d'identification de sécurité importante. Ne partagez la clé secrète client avec personne et ne la distribuez pas dans une application cliente.
	

## <a name="secrets"> </a>Ajout des informations de compte Microsoft à votre application


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Microsoft Account**. Paste in the values you obtained earlier and click **Save**.


7. Revenez au [portail de gestion Azure] et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification / Autorisation**.

8. Si la fonction Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **Activer**.

9. Cliquez sur **Compte Microsoft**. Collez les valeurs d’ID de l’application et App Secret que vous avez obtenues précédemment et activez éventuellement les étendues que votre application requiert. Cliquez ensuite sur **OK**.

    ![][1]
	
11. Par défaut, App Service fournit la connexion, mais ne restreint pas l’accès au contenu de votre site et des API : c’est la responsabilité de votre code d’application. Si vous souhaitez que le site soit complètement protégé par une connexion via un compte Microsoft, modifiez le choix dans le menu déroulant **Action to take when request is not authenticated (Action à entreprendre lorsque la demande n’est pas authentifiée)** et choisissez l’option **Microsoft Account (Compte Microsoft)**. Ainsi, toutes les demandes devront être authentifiées ; les demandes non authentifiées seront redirigées vers l’écran de connexion avec le compte Microsoft.

11. Cliquez sur **Save**.


Vous êtes maintenant prêt à utiliser un compte Microsoft pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[portail de gestion Azure]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->