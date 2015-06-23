<properties 
	pageTitle="Prise en main de l'authentification (Windows Store) | Centre de développement mobile" 
	description="Découvrez comment utiliser Mobile Services pour authentifier les utilisateurs de votre application Windows Store via divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Ajout de l'authentification à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)] 

Cette rubrique explique comment authentifier les utilisateurs dans Azure Mobile Services à partir d'une application Windows universelle. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscription de votre application pour l'authentification et configuration de Mobile Services]
2. [Restriction des autorisations de table pour les utilisateurs authentifiés]
3. [Ajout de l'authentification à l'application]
5. [Stockage de jetons d'authentification sur le client]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

>[AZURE.NOTE]Ce didacticiel vous explique comment authentifier les utilisateurs dans les applications Windows Store et Windows Phone Store 8.1. Pour une application Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulter cette version de [Prise en main de l'authentification dans Mobile Services](mobile-services-windows-phone-get-started-users.md).

>Ce didacticiel explique le flux d’authentification géré par Mobile Services via différents fournisseurs d’identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Pour utiliser plutôt Live Connect avec l'authentification gérée par un client afin de fournir une authentification unique dans votre application Windows Phone, consultez la rubrique [Authentifier votre application du Windows Store avec l'authentification gérée par un client en utilisant un compte Microsoft](mobile-services-windows-store-dotnet-single-sign-on.md). L'authentification gérée par un client permet à votre application d'accéder à des données supplémentaires détenues par le fournisseur d'identité. Vous pouvez obtenir les mêmes données utilisateur dans votre service mobile en appelant la fonction **user.getIdentities()** dans des scripts serveur. Pour plus d'informations, consultez [ce billet](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Inscription de votre application pour l'authentification et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)] 
 
>[AZURE.NOTE]Lorsque vous utilisez les outils Visual Studio pour connecter votre application à un service mobile, l'outil génère deux ensembles de définitions **MobileServiceClient**, une pour chaque plateforme cliente. C'est le bon moment pour simplifier le code généré en unifiant les définitions **MobileServiceClient** encapsulées dans `#if...#endif` en une seule définition encapsulée, utilisée par les deux versions de l'application. Cette opération n'est pas nécessaire si vous avez téléchargé l'application de démarrage rapide à partir du portail de gestion Azure.

##<a name="add-authentication"></a> Ajout de l'authentification à l'application

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

À présent, tout utilisateur authentifié par vos fournisseurs d'identité approuvés peut accéder à la table *TodoItem*. Pour mieux sécuriser les données spécifiques à l'utilisateur, vous devez également implémenter l'autorisation. Pour ce faire, vous obtenez l'ID utilisateur d'un utilisateur donné, qui permettra de déterminer le niveau d'accès dont cet utilisateur doit disposer pour une ressource donnée.

##<a name="tokens"></a>Stockage du jeton d'authentification sur le client

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services](mobile-services-javascript-backend-service-side-authorization.md), vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Obtenez plus d'informations sur Mobile Services avec .NET dans [le guide de fonctionnement Mobile Services .NET]

<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
[Ajout de l'authentification à l'application]: #add-authentication
[Stockage de jetons d'authentification sur le client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Prise en main de Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: mobile-services-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[le guide de fonctionnement Mobile Services .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=54--> 