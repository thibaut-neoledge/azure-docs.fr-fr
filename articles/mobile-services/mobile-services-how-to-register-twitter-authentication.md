<properties 
	pageTitle="Inscription pour l&#39;authentification Twitter | Microsoft Azure" 
	description="Découvrez comment utiliser l&#39;authentification Twitter avec votre application Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/08/2015" 
	ms.author="glenga"/>

#Inscrire des applications pour la connexion à Twitter avec Mobile Services

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

Cette rubrique montre comment inscrire vos applications afin d'utiliser Twitter pour l'authentification auprès d'Azure Mobile Services.

>[AZURE.NOTE]Ce didacticiel concerne [Azure Mobile Services](http://azure.microsoft.com/services/mobile-services/), une solution conçue pour vous aider à créer des applications mobiles évolutives pour n'importe quelle plateforme. Grâce à Mobile Services, synchronisez des données, authentifiez des utilisateurs et envoyer des notifications Push en toute simplicité. Cette page est consacrée au didacticiel <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started-users/">Prise en main de l'authentification</a>, qui montre comment permettre aux utilisateurs de se connecter à votre application. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel <a href="http://azure.microsoft.com/documentation/articles/mobile-services-ios-get-started/">Prise en main de Mobile Services</a>.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Twitter avec une adresse électronique vérifiée. Pour créer un compte Twitter, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Rendez-vous sur le site web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter Developers</a>, connectez-vous avec votre compte Twitter, puis cliquez sur **Create a new application**.

   	![][1]

2. Tapez les valeurs **Name**, **Description** et **Website** correspondant à votre application, puis tapez l'URL du service mobile suivie du chemin _/login/twitter_ dans **Callback URL**.

	>[AZURE.NOTE]Pour un service mobile de backend .NET publié sur Azure avec Visual Studio, l'URL de redirection est l'URL de votre service mobile, suivie du chemin _signin-twitter_. Dans cet exemple, notre service mobile aurait l'URL de rappel ```https://todolist.azure-mobile.net/signin-twitter```.

   	![][2]

3.  En bas de la page, lisez les conditions et acceptez-les, puis cliquez sur **Create your Twitter application**.

   	Cette opération inscrit l'application et affiche les détails de la demande.

6. Cliquez sur l'onglet **Keys and Access Tokens** dans le tableau de bord de votre application et prenez note des valeurs **Consumer key** et **Consumer secret**.

    > [AZURE.NOTE]La clé secrète consommateur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

7. Cliquez sur l'onglet **Settings**, faites défiler vers le bas et vérifiez que la case **Allow this application to be used to sign in with Twitter** est cochée, puis cliquez sur **Update Settings**.

Vous pouvez désormais utiliser une connexion Twitter pour l'authentification dans votre application en fournissant les valeurs de clé et de secret à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO8-->