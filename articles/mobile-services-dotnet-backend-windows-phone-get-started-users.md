<properties 
	pageTitle="Prise en main de l'authentification (Windows Phone) | Centre de développement mobile" 
	description="Découvrez comment utiliser Mobile Services pour authentifier les utilisateurs de votre application Windows Phone via divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="glenga"/>

# Ajout d'une authentification à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir d'une application Windows Phone. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

>[AZURE.NOTE] Ce didacticiel concerne les applications Windows Phone 8.0 et Windows Phone 8.1 Silverlight. Si vous avez une application Windows Phone Store 8.1 ou une application Windows universelle, conformez-vous à la place à la [version d'application Windows universelle de cette rubrique](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscription de votre application pour l'authentification et configuration de Mobile Services]
2. [Restriction des autorisations de table pour les utilisateurs authentifiés]
3. [Ajout de l'authentification à l'application]
4. [Stockage de jetons d'authentification sur le client]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez également commencer par suivre le didacticiel [Prise en main de Mobile Services]. 


##<a name="register"></a> Inscription de votre application pour l'authentification et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>Dans Visual Studio, ouvrez votre projet d'application cliente et assurez-vous que dans le fichier App.xaml.cs, l'instance de **MobileServiceClient** est configurée pour utiliser l'URL du cloud sur le service mobile.</li> 
<li><p>Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.</p>
   
   	<p>Cela se produit, car l'application tente d'accéder à Mobile Services en tant qu'utilisateur non authentifié, alors que la table <em>TodoItem</em> requiert désormais l'authentification.</p></li>
</ol>

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a>Ajout de l'authentification à l'application

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Stockage de jetons d'authentification sur le client

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Autorisation des utilisateurs avec des scripts], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Obtenez plus d'informations sur Mobile Services avec .NET dans le [Guide de fonctionnement Mobile Services .NET]

<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
[Ajout de l'authentification à l'application]: #add-authentication
[Stockage de jetons d'authentification sur le client]: #tokens
[Étapes suivantes]: #next-steps


<!-- URLs. -->
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Authentification unique pour les applications Windows Phone à l'aide de Live Connect]: /fr-fr/documentation/articles/mobile-services-windows-phone-single-sign-on
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[Autorisation des utilisateurs avec des scripts]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts
[JavaScript et HTML]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library
[Inscription du package de votre application Windows Store pour l'authentification Microsoft]: /fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


<!--HONumber=42-->
