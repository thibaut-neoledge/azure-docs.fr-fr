<properties 
	pageTitle="Inscription pour l'authentification Google - Mobile Services" 
	description="Découvrez comment inscrire vos applications afin d'utiliser Google pour l'authentification auprès d'Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/11/2015" 
	ms.author="glenga"/>

# Inscription des applications pour la connexion Google avec Mobile Services

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Cette rubrique montre comment inscrire vos applications afin d'utiliser Google pour l'authentification auprès d'Azure Mobile Services.

>[AZURE.NOTE]Ce didacticiel concerne [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/), une solution conçue pour vous aider à créer des applications mobiles évolutives pour n'importe quelle plateforme. Grâce à Mobile Services, synchronisez des données, authentifiez des utilisateurs et envoyer des notifications Push en toute simplicité. Cette page est consacrée au didacticiel [Prise en main de l'authentification](mobile-services-ios-get-started-users.md), qui montre comment connecter des utilisateurs à votre application. <br/>Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services](mobile-services-ios-get-started.md).

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Google avec une adresse de messagerie vérifiée. Pour créer un compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Rendez-vous sur le site web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">API Google</a>, connectez-vous avec votre compte Google, cliquez sur **Créer un projet**, entrez un **Nom de projet**, puis cliquez sur **Créer**.

   	![Nouveau projet d'API Google](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png)

2. Cliquez sur **Écran de consentement**, sélectionnez votre **Adresse de messagerie**, entrez un **Nom de produit**, puis cliquez sur **Enregistrer**.

3. Cliquez sur **API et authentification** > **Informations d'identification** > **Créer un ID de client**.

   	![Créer un ID de client](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png)

4. Sélectionnez **Application Web**, tapez l'URL de votre service mobile dans **Origines JavaScript autorisées**, remplacez l'URL générée dans **URI de redirection autorisé** par l'URL de votre service mobile suivie de `/login/google`, puis cliquez sur **Créer un ID de client**.

	>[AZURE.NOTE]Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin _signin-google_ de votre service mobile en tant que service .NET, par exemple `https://todolist.azure-mobile.net/signin-google`.

   	![](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png)

5. Sous **Client ID for web applications**, notez les valeurs de **Client ID** et **Client secret**.

   	![Informations d'identification du client](./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png)

    >[AZURE.IMPORTANT]La clé secrète client est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

Vous êtes désormais prêt à configurer votre service mobile pour utiliser l'authentification Google dans votre application.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->