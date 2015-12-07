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
	ms.date="11/20/2015"
	ms.author="mahender"/>

# Comment configurer votre application App Service de manière à utiliser la connexion via Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser Facebook comme fournisseur d'authentification.

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com].

> [AZURE.NOTE]Cette rubrique décrit l'utilisation de la fonctionnalité Authentification/autorisation d'App Service. Elle remplace la passerelle App Service pour la plupart des applications. Les différences qui s'appliquent à l'utilisation de la passerelle sont signalées dans des notes tout au long de cette section.


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

10. Si vous ne voyez pas de section de site web ci-dessous, cliquez sur **Add Platform** et sélectionnez **Website**. Entrez l’**URL** de votre application Mobile App dans le champ **Site URL**, puis cliquez sur **Save Changes**.

11. Cliquez sur l’onglet **Advanced** et ajoutez l’**URI de redirection** de votre application dans **Valid OAuth redirect URIs**. Ensuite, cliquez sur **Save Changes**. Votre URI de redirection correspond à l’URL de votre application suivie du chemin d’accès, _/.auth/login/facebook/callback_. Par exemple : `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Assurez-vous d'utiliser le schéma HTTPS.


	> [AZURE.NOTE]Si vous utilisez la passerelle App Service au lieu de la fonction d’authentification/autorisation d’App Service, votre URL de redirection utilise à la place l'URL de la passerelle avec le chemin d’accès _/signin-facebook_.


12. Le compte Facebook qui a été utilisé pour inscrire l'application est un administrateur de l'application. À ce stade, seuls les administrateurs peuvent se connecter à cette application. Pour authentifier d'autres comptes Facebook, cliquez sur **Status & Review (État et révision)** dans la barre de navigation à gauche. Cliquez ensuite sur **Oui** pour autoriser l'accès public général.


## <a name="secrets"> </a>Ajout des informations Facebook à votre application

> [AZURE.NOTE]Si vous utilisez la passerelle App Service, ignorez cette section et accédez à votre passerelle dans le portail. Sélectionnez **Paramètres**, **Identité**, puis **Facebook**. Collez les valeurs obtenues précédemment et cliquez sur **Enregistrer**.


13. Revenez au [portail de gestion Azure] et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification / Autorisation**.

14. Si la fonctionnalité Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **On**.

15. Cliquez sur **Facebook**. Collez les valeurs d'ID de l'application et App Secret que vous avez obtenues précédemment et activez éventuellement les étendues que votre application requiert. Cliquez ensuite sur **OK**.

    ![][1]
	
	Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.

17. (Facultatif) Pour restreindre l'accès à votre site aux seuls utilisateurs authentifiés par Facebook, définissez **Action à exécuter lorsque la demande n'est pas authentifiée** sur **Facebook**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Facebook pour être authentifiées.

17. Cliquez sur **Enregistrer**.

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

<!---HONumber=AcomDC_1125_2015-->