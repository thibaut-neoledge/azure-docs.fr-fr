<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Inscription de vos applications pour utiliser les informations d'identification d'un compte Microsoft

Cette rubrique montre comment inscrire votre application afin d'utiliser Live Connect comme fournisseur d'authentification pour Azure Mobile Services.

> [WACOM.NOTE]Pour configurer l'authentification de compte Microsoft pour une application Windows 8.1 ou Windows Phone 8.1,[consultez la rubrique Inscription du package de votre application Windows Store pour l'authentification Microsoft][].

1.  Accédez à la page [Mes applications][] dans le Centre des développeurs Live Connect, puis connectez-vous avec votre compte Microsoft si nécessaire.

2.  Cliquez sur **Créer une application**, puis tapez le **Nom de l'application** et cliquez sur **J'accepte**.

    ![][]

    Cette action inscrit l'application auprès de Live Connect.

3.  Cliquez sur **Paramètres de l'API**, fournissez une valeur de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` dans **URL de redirection**, puis cliquez sur **Enregistrer**.

    > [WACOM.NOTE]Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin signin-google de votre service mobile en tant que service .NET, par exemple `https://todolist.azure-mobile.net/signin-microsoft`.

    ![][1]

    Cette action active l'authentification de compte Microsoft dans l'application.

    > [WACOM.NOTE]Pour une inscription d'application Live Connect existante, vous devez d'abord activer **Sécurité de redirection améliorée**.

4.  Cliquez sur **Paramètres d'application** et notez les valeurs de l'**ID du client** et de la **Clé secrète client**.

    ![][2]

    <div class="dev-callout"><b>Remarque relative &agrave; la s&eacute;curit&eacute;</b>
<p>La cl&eacute; secr&egrave;te client est une information d'identification de s&eacute;curit&eacute; importante. Ne la partagez pas avec quiconque et ne la distribuez pas avec votre application.</p>
</div>

Vous pouvez désormais utiliser un compte Microsoft pour l'authentification dans votre application en fournissant les valeurs de l'ID client et de la clé secrète client à Mobile Services.

  [consultez la rubrique Inscription du package de votre application Windows Store pour l'authentification Microsoft]: /fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
  [Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  []: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png
  [2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
