<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Inscription des applications pour l'authentification Twitter avec Mobile Services

Cette rubrique montre comment inscrire vos applications afin d'utiliser Twitter pour l'authentification auprès d'Azure Mobile Services.

<div class="dev-callout"><b>Remarque</b>
<p>Pour effectuer la proc&eacute;dure d&eacute;crite dans cette rubrique, vous devez disposer d'un compte Twitter avec une adresse &eacute;lectronique v&eacute;rifi&eacute;e. Pour cr&eacute;er un compte Twitter, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.</p>
</div>

1.  Rendez-vous sur le site Web [Twitter Developers][], connectez-vous avec votre compte Twitter, puis cliquez sur **Create a new application**.

    ![][]

2.  Tapez les valeurs **Name**, **Description** et **Website** correspondant à votre application, puis tapez l'URL du service mobile suivie du chemin */login/twitter* dans **Callback URL**.

    > [WACOM.NOTE]Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin *signin-twitter* de votre service mobile en tant que service .NET, par exemple `https://todolist.azure-mobile.net/signin-twitter`.

    ![][1]

3.  En bas de la page, lisez les conditions et acceptez-les, tapez les mots affichés dans le CAPTCHA, puis cliquez sur **Create your Twitter application**.

    ![][2]

    Cette opération inscrit l'application et affiche les détails de la demande.

4.  Notez les valeurs de **Consumer key** et **Consumer secret**.

    ![][3]

    <div class="dev-callout"><b>Remarque relative &agrave; la s&eacute;curit&eacute;</b>
<p>La cl&eacute; secr&egrave;te consommateur est une information d'identification de s&eacute;curit&eacute; importante. Ne partagez pas cette cl&eacute; secr&egrave;te avec quiconque et ne la distribuez pas avec votre application.</p>
</div>

5.  Cliquez sur l'onglet **Settings**, faites défiler vers le bas et activez l'option **Allow this application to be used to sign in with Twitter**, puis cliquez sur **Update this Twitter application's settings**.

    ![][4]

Vous pouvez désormais utiliser une connexion Twitter pour l'authentification dans votre application en fournissant les valeurs de clé et de secret à Mobile Services.

  [twitter.com]: http://go.microsoft.com/fwlink/p/?LinkID=268287
  [Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  []: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
  [1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
  [2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
  [3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
  [4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png
