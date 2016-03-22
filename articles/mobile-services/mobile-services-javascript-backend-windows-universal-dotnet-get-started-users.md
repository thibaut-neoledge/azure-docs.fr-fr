<properties 
	pageTitle="Ajouter l’authentification à votre application Windows 8.1 universelle | Azure Mobile Services"
	description="Découvrez comment utiliser Mobile Services pour authentifier les utilisateurs de votre application Windows Store via divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/05/2016" 
	ms.author="glenga"/>

# Ajouter l’authentification à votre application Windows 8.1 universelle

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez [Ajout de l’authentification à votre application Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md).

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir d’une application Windows 8.1 universelle. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

>[AZURE.NOTE]Ce didacticiel vous explique comment authentifier les utilisateurs dans les applications Windows Store et Windows Phone Store 8.1. Pour une application Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulter cette version de [Prise en main de l'authentification dans Mobile Services](mobile-services-windows-phone-get-started-users.md).

##<a name="register"></a> Inscrire votre application pour l'authentification et configurer Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a> Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]
 
>[AZURE.NOTE] Lorsque vous utilisez les outils Visual Studio pour connecter votre application à un service mobile, l'outil génère deux ensembles de définitions **MobileServiceClient**, une pour chaque plateforme cliente. C'est le bon moment pour simplifier le code généré en unifiant les définitions **MobileServiceClient** encapsulées dans `#if...#endif` en une seule définition encapsulée, utilisée par les deux versions de l'application. Cette opération n’est pas nécessaire si vous avez téléchargé l’application de démarrage rapide à partir du [portail Azure Classic].

##<a name="add-authentication"></a>Ajouter l'authentification à l'application

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

À présent, tout utilisateur authentifié par vos fournisseurs d'identité approuvés peut accéder à la table *TodoItem*. Pour mieux sécuriser les données spécifiques à l'utilisateur, vous devez également implémenter l'autorisation. Pour ce faire, vous obtenez l'ID utilisateur d'un utilisateur donné, qui permettra de déterminer le niveau d'accès dont cet utilisateur doit disposer pour une ressource donnée.

##<a name="tokens"></a>Stockage du jeton d'authentification sur le client

L'exemple précédent montrait une connexion standard, qui nécessite que le client contacte le fournisseur d'identité et le service mobile à chaque démarrage de l'application. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d’utilisation si de nombreux clients tentent de lancer votre application en même temps. Une meilleure approche consiste à mettre en cache le jeton d'autorisation renvoyé par Mobile Services et à l'utiliser en premier avant de faire appel à la connexion via un fournisseur.

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par Mobile Services, que vous utilisiez l'authentification gérée par un client ou gérée par un service. Ce didacticiel utilise cette dernière.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services](mobile-services-javascript-backend-service-side-authorization.md), vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

##Voir aussi

+ [Fonctionnalité d’utilisateurs améliorés](http://go.microsoft.com/fwlink/p/?LinkId=506605)<br/> Vous pouvez obtenir d’autres données utilisateur gérées par le fournisseur d’identité dans votre service mobile en appelant la fonction **user.getIdentities()** dans les scripts serveur. 

+ [Guide de fonctionnement Mobile Services .NET] <br/>En savoir plus sur l'utilisation de Mobile Services avec un client .NET.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Prise en main de Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with authentication]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-dotnet-authorize-users-in-scripts.md

[portail Azure Classic]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
 

<!---------HONumber=AcomDC_0309_2016-->