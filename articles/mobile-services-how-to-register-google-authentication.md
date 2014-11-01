<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Inscription des applications pour la connexion Google avec Mobile Services

Cette rubrique montre comment inscrire vos applications afin d'utiliser Google pour l'authentification auprès d'Azure Mobile Services.

<div class="dev-callout"><b>Remarque</b>
<p>Pour effectuer la proc&eacute;dure d&eacute;crite dans cette rubrique, vous devez disposer d'un compte Google avec une adresse &eacute;lectronique v&eacute;rifi&eacute;e. Pour cr&eacute;er un compte Google, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div>

1.  Rendez-vous sur le site Web [API Google][], connectez-vous avec votre compte Google, cliquez sur **Créer un projet**, saisissez un **Nom de projet**, puis cliquez sur **Créer**.

    ![][]

2.  Cliquez sur **API et authentification**, sur **Informations d'identification**, puis sur **Créer un ID de client**.

    ![][1]

3.  Sélectionnez **Application Web**, saisissez l'URL de votre service mobile dans **Origines Javascript autorisées**, remplacez l'URL générée dans **URI de redirection autorisé** par l'URL de votre service mobile suivie de */login/google*, puis cliquez sur **Créer un ID de client**.

    > [WACOM.NOTE]Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin signin-google de votre service mobile en tant que service .NET, par exemple https://todolist.azure-mobile.net/signin-google.

    ![][2]

4.  Sous **Client ID for web applications**, notez les valeurs de **Client ID** et **Client secret**.

    ![][3]

    <div class="dev-callout"><b>Remarque relative &agrave; la s&eacute;curit&eacute;</b>
<p>La cl&eacute; secr&egrave;te client est une information d'identification de s&eacute;curit&eacute; importante. Ne partagez pas cette cl&eacute; secr&egrave;te avec quiconque et ne la distribuez pas avec votre application.</p>
</div>

Vous pouvez désormais utiliser une connexion Google pour l'authentification dans votre application en fournissant les valeurs de l'ID client et de la clé secrète client à Mobile Services.

  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [API Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  []: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
