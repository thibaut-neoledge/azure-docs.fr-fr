<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Register your apps for Google login with Mobile Services" authors="" solutions="" manager="" editor="" />

Inscription des applications pour la connexion Google avec Mobile Services
==========================================================================

Cette rubrique montre comment inscrire vos applications afin d'utiliser Google pour l'authentification auprès d'Azure Mobile Services.

**Remarque**

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Google avec une adresse électronique vérifiée. Pour créer un compte Google, consultez la page [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Rendez-vous sur le site Web [API Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), connectez-vous avec votre compte Google, puis cliquez sur **Create project...**.

	![][1]   

2.  Cliquez sur **API Access**, puis sur **Create an OAuth 2.0 client ID...**.

	![][2]

3.  Sous **Branding Information**, tapez le nom de votre produit dans **Product name**, puis cliquez sur **Next**.

	![][3]

4.  Sous **Client ID Settings**, sélectionnez **Web application**, tapez l'URL de votre service mobile dans **Your site or hostname**, cliquez sur **more options**, remplacez l'URL générée dans **Authorized Redirect URIs** par l'URL de votre service mobile à laquelle vous ajoutez le chemin */login/google*, puis cliquez sur **Create client ID**.

	![][4]

    > [WACOM.NOTE]Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin *signin-google* de votre service mobile en tant que service .NET, par exemple `https://todolist.azure-mobile.net/signin-google`.

5.  Sous **Client ID for web applications**, notez les valeurs de **Client ID** et **Client secret**.

	![][5]

    **Remarque relative à la sécurité**

    La clé secrète client est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

Vous pouvez désormais utiliser une connexion Google pour l'authentification dans votre application en fournissant les valeurs de l'ID client et de la clé secrète client à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-developers.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
