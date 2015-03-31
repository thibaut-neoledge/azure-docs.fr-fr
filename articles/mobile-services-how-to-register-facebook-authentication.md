<properties 
	pageTitle="Inscription pour l'authentification Facebook - Mobile Services" 
	description="Apprenez à utiliser l'authentification Facebook dans votre application Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Inscription des applications pour l'authentification Facebook avec Mobile Services

Cette rubrique montre comment inscrire vos applications afin d'utiliser Facebook pour l'authentification auprès d'Azure Mobile Services. 

> [AZURE.NOTE] Ce didactiel concerne [Azure Mobile Services], une solution conçue pour vous aider à créer des applications mobiles évolutives pour n'importe quelle plateforme. Grâce à Mobile Services, synchronisez des données, authentifiez des utilisateurs et envoyer des notifications Push en toute simplicité. Cette page s'appuie sur le didacticiel <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Prise en main de l'authentification</a> qui montre comment connecter des utilisateurs à votre application. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Prise en main de Mobile Services</a>.
	
Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>.

1. Accédez au site Web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268286" target="_blank">Développeurs Facebook</a> et connectez-vous à l'aide de vos informations d'identification de compte Facebook.

2. (Facultatif) Si vous n'êtes pas encore inscrit, cliquez sur **My Apps**, puis sur **Register as a Developer**, acceptez la politique et suivez les étapes de l'inscription. 

   	![][0]

3. Cliquez sur **Applications**, puis sur **Créer une application**.

   	![][1]

4. Choisissez un nom unique pour l'application, sélectionnez **Applications pour les Pages**, cliquez sur **Créer une application**, puis répondez à la question de sécurité.

   	![][2]

	Votre application est inscrite sur Facebook. 

5. Cliquez sur **Paramètres**, tapez le domaine de votre service mobile dans **Domaines d'application**. Indiquez également une **Adresse e-mail de contact**, puis cliquez sur **Ajouter une plateforme** et sélectionnez **Site Web**.

   	![][3]

6. Tapez l'URL de votre service mobile dans **URL du Site**, puis cliquez sur **Enregistrer les modifications**.

	![][4]

7. Cliquez sur **Afficher**, indiquez votre mot de passe si vous y êtes invité, puis notez les valeurs **ID de l'application** et **Clé secrète**. 

   	![][5]

	> [AZURE.NOTE] **Remarque relative à la sécurité**
	La clé secrète de l'application est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.


8. Cliquez sur l'onglet **Avancé**, saisissez l'URL de votre service mobile suivie du chemin _/login/facebook_ dans **URI de redirection valide OAuth**, puis cliquez sur **Enregistrer les modifications**. 

	> [AZURE.NOTE] Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin _signin-facebook_ de votre service mobile en tant que service .NET, par exemple <code>https://todolist.azure-mobile.net/signin-facebook</code>.  
	
	![][7]

9. Le compte Facebook pour lequel vous avez défini la nouvelle application est un administrateur de l'application et peut y accéder en tant que tel. Pour authentifier d'autres comptes Facebook, ils doivent accéder à l'application. Cette étape accorde l'accès public général, de manière à ce que l'application puisse authentifier d'autres comptes Facebook. Cliquez sur **Statut et vérification**. Cliquez ensuite sur **Oui** pour autoriser l'accès public général.

    ![][6]



Vous pouvez désormais utiliser une connexion Facebook pour l'authentification de votre application en fournissant les valeurs de l'ID de l'application et de la clé secrète de l'application à Mobile Services.  

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png
[6]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-general-public.png
[7]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-3.png

<!-- URLs. -->
[Développeurs Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Prise en main de l'authentification]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!--HONumber=47-->
