<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="" documentationCenter="" title="Register your apps for Twitter login with Mobile Services" authors="" solutions="" manager="" editor="" />

Inscription des applications pour l'authentification Twitter avec Mobile Services
=================================================================================

Cette rubrique montre comment inscrire vos applications afin d'utiliser Twitter pour l'authentification auprès d'Azure Mobile Services.

**Remarque**

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Twitter avec une adresse électronique vérifiée. Pour créer un compte Twitter, consultez la page [twitter.com](http://go.microsoft.com/fwlink/p/?LinkID=268287).

1.  Rendez-vous sur le site Web [Twitter Developers](http://go.microsoft.com/fwlink/p/?LinkId=268300), connectez-vous avec votre compte Twitter, puis cliquez sur **Create a new application**.

	![][1]

2.  Tapez les valeurs **Name**, **Description** et **Web Site** correspondant à votre application, puis tapez l'URL du service mobile dans **Callback URL**.

	![][2]

    > [WACOM.NOTE]Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin *signin-twitter* de votre service mobile en tant que service .NET, par exemple `https://todolist.azure-mobile.net/signin-twitter`.
    > La valeur **Web Site** est obligatoire, mais n'est pas utilisée.

3.  En bas de la page, lisez les conditions et acceptez-les, tapez les mots affichés dans le CAPTCHA, puis cliquez sur **Create your Twitter application**.
![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png) 
	
	Cette opération inscrit l'application et affiche les détails de la demande.

1.  Notez les valeurs de **Consumer key** et **Consumer secret**.

	![][4]

    **Remarque relative à la sécurité**

    La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

2.  Cliquez sur l'onglet **Settings**, faites défiler vers le bas et activez l'option **Allow this application to be used to sign in with Twitter**, puis cliquez sur **Update this Twitter application's settings**.

    ![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png)

Vous pouvez désormais utiliser une connexion Twitter pour l'authentification dans votre application en fournissant les valeurs de clé et de secret à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
