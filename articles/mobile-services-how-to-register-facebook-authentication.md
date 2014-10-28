<properties linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Inscription des applications pour l'authentification Facebook avec Mobile Services

Cette rubrique montre comment inscrire vos applications afin d'utiliser Facebook pour l'authentification auprès d'Azure Mobile Services.

> [WACOM.NOTE] Ce didacticiel est consacré à [Azure Mobile Services][], une solution permettant de créer des applications mobiles évolutives pour n'importe quelle plateforme. Grâce à Mobile Services, synchronisez des données, authentifiez des utilisateurs et envoyer des notifications Push en toute simplicité. Cette page est consacrée au didacticiel [Prise en main de l'authentification][], qui montre comment permettre aux utilisateurs de se connecter à votre application. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services][].

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com][].

1.  Accédez au site Web [Développeurs Facebook][] et connectez-vous à l'aide des informations d'identification de votre compte Facebook.

2.  (Facultatif) Si vous n'êtes pas encore inscrit, cliquez sur **Applications**, puis sur **Register as a Developer**, acceptez la politique et suivez les étapes de l'inscription.

    ![][]

3.  Cliquez sur **Applications**, puis sur **Créez une application**.

    ![][1]

4.  Choisissez un nom unique pour l'application, sélectionnez **Applications pour les Pages**, cliquez sur **Créer une application**, puis répondez à la question de sécurité.

    ![][2]

    Votre application est inscrite sur Facebook.

5.  Cliquez sur **Paramètres**, tapez le domaine de votre service mobile dans **Domaines d'application**. Saisissez également une **Adresse e-mail de contact**, cliquez sur **Ajouter une plateforme** et sélectionnez **Site Web**.

    ![][3]

6.  Tapez l'URL du service mobile dans **Site URL**, puis cliquez sur **Enregistrer les modifications**.

    ![][4]

7.  Cliquez sur **Afficher**, indiquez votre mot de passe si vous y êtes invité, puis notez les valeurs **Identifiant de l'application** et **Clé secrète**.

    ![][5]

    <div class="dev-callout"><b>Remarque relative &agrave; la s&eacute;curit&eacute;</b>
<p>La cl&eacute; secr&egrave;te de l'application est une information d'identification de s&eacute;curit&eacute; importante. Ne partagez pas cette cl&eacute; secr&egrave;te avec quiconque et ne la distribuez pas avec votre application.</p>
</div>

8.  Cliquez sur l'onglet **Avancé**, saisissez l'URL de votre service mobile suivie du chemin */login/facebook* dans **Valid OAuth redirect URIs**, puis cliquez sur **Enregistrer les modifications**.

    > [WACOM.NOTE]Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin *signin-facebook* de votre service mobile en tant que service .NET, par exemple `https://todolist.azure-mobile.net/signin-facebook`.

    ![][6]

9.  Le compte Facebook pour lequel vous avez défini la nouvelle application est un administrateur de l'application et peut y accéder en tant que tel. Pour authentifier d'autres comptes Facebook, ils doivent accéder à l'application. Cette étape accorde l'accès public général, de manière à ce que l'application puisse authentifier d'autres comptes Facebook. Cliquez sur **Statut et vérification**. Cliquez ensuite sur **Oui** pour autoriser l'accès public général.

    ![][7]

Vous pouvez désormais utiliser une connexion Facebook pour l'authentification de votre application en fournissant les valeurs de l'ID de l'application et de la clé secrète de l'application à Mobile Services.

  [Azure Mobile Services]: http://azure.microsoft.com/fr-fr/services/mobile-services/
  [Prise en main de l'authentification]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-ios-get-started-users/
  [Prise en main de Mobile Services]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-ios-get-started/
  [facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
  [Développeurs Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
  []: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
  [1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
  [2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
  [3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
  [4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
  [5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
  [6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png
  [7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
