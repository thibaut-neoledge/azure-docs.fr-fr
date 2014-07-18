<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Inscription des applications pour l'authentification Facebook avec Mobile Services
==================================================================================

Cette rubrique montre comment inscrire vos applications afin d'utiliser Facebook pour l'authentification auprès d'Azure Mobile Services.

**Remarque**

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285).

1.  Accédez au site Web [Développeurs Facebook](http://go.microsoft.com/fwlink/p/?LinkId=268286) et connectez-vous à l'aide des informations d'identification de votre compte Facebook.

2.  (Facultatif) Si vous n'êtes pas encore inscrit, cliquez sur **Applications**, puis sur **Register as a Developer**, acceptez la politique et suivez les étapes de l'inscription.

	![][0]

3.  Cliquez sur **Applications**, puis sur **Créez une application**.

	![][1]

4.  Choisissez un nom unique pour l'application, sélectionnez **Applications pour les Pages**, cliquez sur **Créer une application**, puis répondez à la question de sécurité.

	![][2]

    Votre application est inscrite sur Facebook.

5.  Cliquez sur **Paramètres**, tapez le domaine de votre service mobile dans **App Domains**, puis cliquez sur **Ajouter une plateforme** et sélectionnez **Site Web**.

	![][3]

6.  Tapez l'URL du service mobile dans **Site URL**, puis cliquez sur **Enregistrer les modifications**.

    ![](./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png)

7.  Cliquez sur **Afficher**, indiquez votre mot de passe si vous y êtes invité, puis notez les valeurs **Identifiant de l'application** et **Clé secrète**.

	![][5]

    **Remarque relative à la sécurité**

    La clé secrète de l'application est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

Vous pouvez désormais utiliser une connexion Facebook pour l'authentification de votre application en fournissant les valeurs de l'ID de l'application et de la clé secrète de l'application à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Management Portal]: https://manage.windowsazure.com/
