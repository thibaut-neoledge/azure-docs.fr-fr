<properties pageTitle="Inscription pour l'authentification Microsoft - Mobile Services" description="Découvrez comment vous inscrire pour l'authentification Microsoft dans votre application Azure Mobile Services." authors="ggailey777" services="mobile-services" documentationCenter="" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# Inscription de vos applications pour utiliser les informations d'identification d'un compte Microsoft

Cette rubrique montre comment inscrire votre application afin d'utiliser Live Connect comme fournisseur d'authentification pour Azure Mobile Services. 

>[AZURE.NOTE]Afin de configurer l'authentification de compte Microsoft pour une application Windows universelle ou fournir une authentification unique pour une application Windows Store, consultez la page [Inscription du package de votre application Windows Store pour l'authentification Microsoft](/fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication).

1. Accédez à la page <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Mes applications</a> dans le Centre des développeurs Live Connect, puis connectez-vous avec votre compte Microsoft si nécessaire. 

2. Cliquez sur **Créer une application**, puis tapez le **Nom de l'application** et cliquez sur **J'accepte**.

   	![][1] 

   	Cette action inscrit l'application auprès de Live Connect.

3. Cliquez sur **Paramètres de l'API**, fournissez une valeur de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` dans **URL de redirection**, puis cliquez sur **Enregistrer**.

	>[AZURE.NOTE]Pour un service mobile principal .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin _signin-microsoft_ de votre service mobile en tant que service .NET, par exemple <code>https://todolist.azure-mobile.net/signin-microsoft</code>.  

	![][3]

	Cela permet d'activer l'authentification du compte Microsoft sur votre application.

	>[AZURE.NOTE]Pour une inscription d'application Live Connect existante, vous devez d'abord activer **Sécurité de redirection améliorée**.

4. Cliquez sur **Paramètres d'application** et notez les valeurs **ID du client** et **Clé secrète client**. 

   	![][2]

    > [AZURE.NOTE] La clé secrète client est une information d'identification de sécurité importante. Ne la partagez pas avec quiconque et ne la distribuez pas avec votre application.

Vous pouvez désormais utiliser un compte Microsoft pour l'authentification dans votre application en fournissant les valeurs de l'ID client et de la clé secrète client à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Portail de gestion Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
