<properties
	pageTitle="Comment configurer l'authentification Facebook pour votre application App Services"
	description="Découvrez comment configurer l'authentification Facebook pour votre application App Services."
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
	ms.date="10/29/2015"
	ms.author="mahender"/>

# Comment configurer votre application App Service de manière à utiliser la connexion via Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser Facebook comme fournisseur d'authentification.

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com].


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Inscription de votre application sur Facebook

1. Connectez-vous au [portail de gestion Azure] et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application Facebook.
 
2. Dans une autre fenêtre de navigateur, accédez au site Web [Développeurs Facebook] et connectez-vous à l’aide des informations d’identification de votre compte Facebook.

3. (Facultatif) Si vous n'êtes pas encore inscrit, cliquez sur **Applications**, puis sur **Register as a Developer**, acceptez la politique et suivez les étapes de l'inscription.

4. Cliquez sur **My Apps**, puis sur **Add a New App**.

5. Sélectionnez **Website** comme plateforme. Choisissez un nom unique pour votre application, puis cliquez sur **Create New Facebook App ID**.

6. Choisissez une catégorie pour votre application dans la liste déroulante. Ensuite, cliquez sur **Create App ID**.

7. Dans la page suivante, sélectionnez **Skip Quick Start** dans le coin supérieur droit. Ceci vous dirigera vers le tableau de bord du développeur pour votre application.

8. Dans le champ **App Secret**, cliquez sur **Show**, fournissez votre mot de passe si nécessaire, puis notez les valeurs **App ID** et **App Secret**. Vous configurerez l’application pour les utiliser plus tard.

	> [AZURE.NOTE]**Remarque relative à la sécurité** : la clé secrète d’application est une information d’identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.

9. Dans la barre de navigation gauche, cliquez sur **Settings**. Tapez l’**URL** de votre application mobile dans **App Domains** et entrez une adresse de messagerie dans **Contact Email**.

    ![][0]

10. Si vous ne voyez pas de section de site web ci-dessous, cliquez sur **Add Platform** et sélectionnez **Website**. Tapez l’**URL** de votre application Mobile App dans le champ **Site URL**, puis cliquez sur **Save Changes**.

11. Cliquez sur l’onglet **Advanced** et ajoutez l’**URI de redirection** de votre application dans **Valid OAuth redirect URIs**. Ensuite, cliquez sur **Save Changes**. Votre URI de redirection correspond à l’URL de votre application suivie du chemin d’accès, _/.auth/login/facebook/callback_. Par exemple : `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Assurez-vous d'utiliser le schéma HTTPS.


	> [AZURE.NOTE]Si vous utilisez la passerelle App Service au lieu de la fonction d’authentification/autorisation d’App Service, votre URL de redirection utilise à la place l'URL de la passerelle avec le chemin d’accès _/signin-facebook_.


12. Le compte Facebook qui a été utilisé pour inscrire l'application est un administrateur de l'application. À ce stade, seuls les administrateurs peuvent se connecter à cette application. Pour authentifier d'autres comptes Facebook, cliquez sur **Status & Review (État et révision)** dans la barre de navigation à gauche. Cliquez ensuite sur **Oui** pour autoriser l'accès public général.


## <a name="secrets"> </a>Ajout des informations Facebook à votre application


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Facebook**. Paste in the values you obtained earlier and click **Save**.


13. Revenez au [portail de gestion Azure] et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentication / Authorization**.

14. Si la fonction Authentication / Authorization n'est pas activée, positionnez le commutateur sur **On**.

15. Cliquez sur **Facebook**. Collez les valeurs d'ID de l'application et App Secret que vous avez obtenues précédemment et activez éventuellement les étendues que votre application requiert. Cliquez ensuite sur **OK**.

    ![][1]
	
16. Par défaut, App Service fournit la connexion, mais ne restreint pas l'accès au contenu de votre site et des API : c'est la responsabilité de votre code d'application. Si vous souhaitez que le site soit complètement protégé par une connexion via Facebook, modifiez le choix dans le menu déroulant **Action to take when request is not authenticated (Action à entreprendre lorsque la demande n'est pas authentifiée** et choisissez l’option **Facebook**. Ainsi, toutes les demandes devront être authentifiées ; les demandes non authentifiées seront redirigées vers l’écran de connexion avec Facebook.

17. Cliquez sur **Save**.


Vous êtes maintenant prêt à utiliser Facebook pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Développeurs Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /fr-FR/develop/mobile/tutorials/get-started-with-users-dotnet/
[portail de gestion Azure]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->