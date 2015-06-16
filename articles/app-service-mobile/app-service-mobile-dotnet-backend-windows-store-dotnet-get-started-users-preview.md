<properties 
	pageTitle="Prise en main de l'authentification pour Mobile Apps dans Windows" 
	description="Découvrez comment utiliser Mobile Apps pour authentifier les utilisateurs de votre application Windows via divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="mahender"/>

# Ajout de l'authentification à votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique montre comment authentifier les utilisateurs d'une application App Service Mobile App à partir de votre application cliente. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par App Service. Une fois l'utilisateur authentifié et autorisé par votre application Mobile App, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel est basé sur le démarrage rapide de Mobile App. Vous devez également commencer par suivre le didacticiel [Prise en main de votre application mobile].

##<a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li>Dans Visual Studio, ouvrez votre projet d’application cliente et assurez-vous que dans le fichier App.xaml.cs, l’instance de <b>MobileServiceClient</b> est configurée pour utiliser l’URL du cloud vers la ressource Mobile App.</li> 
<li><p>Appuyez sur la touche&#160;F5 pour exécuter cette application basée sur le démarrage rapide&#160;; vérifiez qu'une exception non prise en charge avec le code d'état&#160;401 (Non autorisé) est générée après le démarrage de l'application.</p>
   
   	<p>Cette exception se produit, car l’application tente d’accéder à votre code Mobile App en tant qu’utilisateur non authentifié, alors que la table <em>TodoItem</em> requiert désormais une authentification.</p></li>
</ol>

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir de votre service App Service.

##<a name="add-authentication"></a>Ajout de l’authentification à l’application

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)] 


[AZURE.NOTE]Quand vous avez inscrit les informations de package d’application du Windows Store avec App Services, vous devez appeler la méthode <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> en fournissant la valeur <strong>true</strong> pour le paramètre <em>useSingleSignOn</em>. Si vous ne le faites pas, vos utilisateurs seront toujours invités à se connecter à chaque appel de la méthode de connexion.


##<a name="tokens"></a>Stockage du jeton d’authentification sur le client

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)] 


<!-- Anchors. -->
[Register your app for authentication and configure the App Service]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de votre application mobile]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md


[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
[Single sign-on for Windows Store apps by using Live Connect]: ../mobile-services-windows-store-dotnet-single-sign-on.md
<!--HONumber=54--> 