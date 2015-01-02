<properties urlDisplayName="Register for Twitter Authentication" pageTitle="Inscription pour l'authentification Twitter - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

#Inscription des applications pour l'authentification Twitter avec Mobile Services

Cette rubrique montre comment inscrire vos applications afin d'utiliser Twitter pour l'authentification auprès d'Azure Mobile Services.

>[WACOM.NOTE] Ce didacticiel concerne [Azure Mobile Services](http://azure.microsoft.com/fr-fr/services/mobile-services/), une solution qui permet de développer des applications mobiles évolutives pour toutes les plateformes. Grâce à Mobile Services, synchronisez des données, authentifiez des utilisateurs et envoyer des notifications Push en toute simplicité. Cette page s'appuie sur le didacticiel <a href="http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-ios-get-started-users/">Prise en main de l'authentification</a> qui montre comment connecter des utilisateurs à votre application. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel <a href="http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-ios-get-started/">Prise en main de Mobile Services</a>.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Twitter avec une adresse électronique vérifiée. Pour créer un compte Twitter, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Accédez au site web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">pour les développeurs Twitter</a> , connectez-vous avec les informations d'identification de votre compte Twitter, puis cliquez sur **Create a new application**.

   	![][1]

2. Tapez le **Nom**, la **Description** et le **Site Web** correspondant à votre application, puis indiquez l'URL de votre service mobile, suivie du chemin _/login/twitter_ dans **Callback URL**.

	>[WACOM.NOTE]Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin _signin-twitter_ de votre service mobile en tant que service .NET, par exemple <code>https://todolist.azure-mobile.net/signin-twitter</code>.

   	![][2]

3.  En bas de la page, lisez les conditions et acceptez-les, tapez les mots affichés dans le CAPTCHA, puis cliquez sur **Create your Twitter application**. 

   	![][3]

   	Cette opération inscrit l'application et affiche les détails de la demande.

6. Notez les valeurs de **Consumer key** et **Consumer secret**. 

   	![][4]

    <div class="dev-callout"><b>Remarque relative à la sécurité</b>
	<p>La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.</p>
    </div>

7. Cliquez sur l'onglet **Settings**, faites défiler vers le bas et activez l'option **Allow this application to be used to sign in with Twitter**, puis cliquez sur **Update this Twitter application's settings**.

	![][5]

Vous pouvez désormais utiliser une connexion Twitter pour l'authentification dans votre application en fournissant les valeurs de clé et de secret à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Développeurs Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/

[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=35_1-->
