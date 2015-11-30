<properties
	pageTitle="Comment configurer l'authentification Google pour votre application App Services"
	description="Découvrez comment configurer l'authentification Google pour votre application App Services."
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="mahender"/>

# Comment configurer votre application App Service de manière à utiliser la connexion via Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser Google comme fournisseur d’authentification.

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Google avec une adresse de messagerie vérifiée. Pour créer un compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Inscription de votre application avec Google

1. Connectez-vous au [portail de gestion Azure] et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application Google.
 
2. Rendez-vous sur le site Web [API Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), connectez-vous avec votre compte Google, cliquez sur **Créer un projet**, saisissez un **Nom de projet**, puis cliquez sur **Créer**.

3. Dans la barre de navigation à gauche, cliquez sur **API et authentification**, puis, sous **API sociales**, cliquez sur **API Google+** > **Activer l’API**.

4. Cliquez sur **API et authentification** > **Informations d’identification** > **Écran de consentement OAuth**, puis sélectionnez votre **Adresse de messagerie**, entrez un **Nom de produit** et cliquez sur **Enregistrer**.

5. Dans l’onglet **Informations d’identification**, cliquez sur **Ajouter des informations d’identification** > **ID client OAuth 2.0**, puis sélectionnez **Application Web**.

6. Collez l’**URL** App Service que vous avez copiée précédemment dans **Origines JavaScript autorisées**, puis collez l’**URI de redirection** que vous avez copiée précédemment dans **URI de redirection autorisée**. L’URI de redirection correspond à l’URL de votre application suivie du chemin d’accès, _/.auth/login/google/callback_. Par exemple : `https://contoso.azurewebsites.net/.auth/login/google/callback`. Assurez-vous d'utiliser le schéma HTTPS. Cliquez ensuite sur **Créer**.


	> [AZURE.NOTE]Si vous utilisez la passerelle App Service au lieu de la fonction d’authentification/autorisation d’App Service, votre URL de redirection utilise à la place l’URL de la passerelle avec le chemin d’accès _/signin-google_.


7. Sur l'écran suivant, notez les valeurs de l'ID du client et de la clé secrète client.


    > [AZURE.IMPORTANT]La clé secrète client est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.


## <a name="secrets"> </a>Ajout des informations Google à votre application


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Google**. Paste in the values you obtained earlier and click **Save**.


8. Revenez au [portail de gestion Azure] et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification / Autorisation**.

9. Si la fonction Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **Activer**.

10. Cliquez sur **Google**. Collez les valeurs d’ID de l’application et App Secret que vous avez obtenues précédemment et activez éventuellement les étendues que votre application requiert. Cliquez ensuite sur **OK**.

    ![][1]
	
11. Par défaut, App Service fournit la connexion, mais ne restreint pas l’accès au contenu de votre site et des API : c’est la responsabilité de votre code d’application. Si vous souhaitez que le site soit complètement protégé par une connexion via Google, modifiez le choix dans le menu déroulant **Action to take when request is not authenticated (Action à entreprendre lorsque la demande n’est pas authentifiée)** et choisissez l’option **Google**. Ainsi, toutes les demandes devront être authentifiées ; les demandes non authentifiées seront redirigées vers l’écran de connexion avec Google.

12. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Google pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[portail de gestion Azure]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO4-->