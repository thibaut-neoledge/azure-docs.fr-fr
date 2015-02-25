<properties pageTitle="Inscription pour l'authentification Google - Mobile Services" description="Découvrez comment inscrire vos applications afin d'utiliser Google pour l'authentification auprès d'Azure Mobile Services." services="mobile-services" documentationCenter="" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# Inscription des applications pour la connexion Google avec Mobile Services

Cette rubrique montre comment inscrire vos applications afin d'utiliser Google pour l'authentification auprès d'Azure Mobile Services.

>[AZURE.NOTE] Ce didactiel concerne [Azure Mobile Services](http://azure.microsoft.com/fr-fr/services/mobile-services/), une solution conçue pour vous aider à créer des applications mobiles évolutives pour n'importe quelle plateforme. Grâce à Mobile Services, synchronisez des données, authentifiez des utilisateurs et envoyer des notifications Push en toute simplicité. Cette page est consacrée au didacticiel P<a href="http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-ios-get-started-users/">rise en main de l'authentification</a>, qui explique comment permettre aux utilisateurs de se connecter à votre application. S'il s'agit de votre première expérience avec Mobile Services, veuillez suivre le didacticiel <a href="http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-ios-get-started/">Prise en main de Mobile Services</a>.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Google avec une adresse électronique vérifiée. Pour créer un compte Google, rendez-vous sur la page <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Rendez-vous sur le site web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API Google</a>, connectez-vous avec votre compte Google, cliquez sur **Créer un projet**, saisissez un **Nom de projet**, puis cliquez sur **Créer**.

   	![][1]

2. Cliquez sur **Écran de consentement**, sélectionnez votre **Adresse de messagerie**, entrez un **Nom de produit**, puis cliquez sur **Enregistrer**. 

3. Cliquez sur **API et authentification**, sur **Informations d'identification**, puis sur **Créer un ID de client**.

   	![][2]

4. Sélectionnez **Application Web**, saisissez l'URL de votre service mobile dans **Origines Javascript autorisées**, remplacez l'URL générée dans **URI de redirection autorisé** par l'URL de votre service mobile suivie de _/login/google_, puis cliquez sur **Créer un ID de client**.

	>[AZURE.NOTE] Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin _signin-google_ de votre service mobile en tant que service .NET, par exemple <code>https://todolist.azure-mobile.net/signin-google</code>. 

   	![][3]

5. Sous **ID de Client pour les applications web**, prenez note des valeurs de **ID Client** et **Clé secrète client**. 

   	![][4]

    > [AZURE.IMPORTANT] La clé secrète client est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

Vous pouvez désormais utiliser une connexion Google pour l'authentification dans votre application en fournissant les valeurs de l'ID client et de la clé secrète client à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[API Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/

[Portail de gestion Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
