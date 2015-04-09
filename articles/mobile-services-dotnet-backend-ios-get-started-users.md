<properties
	pageTitle="Ajout de l'authentification à une application Azure Mobile Services (iOS) existante | Centre de développement mobile"
	description="Apprenez à utiliser Mobile Services pour authentifier les utilisateurs de votre application iOS par l'intermédiaire de divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="2/16/2015"
	ms.author="krisragh"/>

# Ajout de l'authentification à une application Azure Mobile Services existante

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide en faisant appel à un fournisseur d'identité pris en charge. Ce didacticiel est basé sur le [didacticiel Démarrage rapide de Mobile Services], que vous devez effectuer en premier.

##<a name="register"></a>Inscription de l'application pour l'authentification et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

Dans Xcode, ouvrez le projet. Appuyez sur le bouton **Exécuter** pour démarrer l'application. Vérifiez qu'une exception avec code d'état 401 (non autorisé) est générée après le démarrage de l'application. Cela se produit, car l'application tente d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ exige à présent l'authentification.

##<a name="add-authentication"></a>Ajout de l'authentification à l'application

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Stockage des jetons d'authentification dans l'application

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services], vous utiliserez la valeur d'ID utilisateur pour filtrer les données retournées.

<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
[Ajout de l'authentification à l'application]: #add-authentication
[Étapes suivantes]:#next-steps
[Stockage des jetons d'authentification dans votre application]:#store-authentication

<!-- URLs. -->
[Autorisation côté service des utilisateurs Mobile Services]: mobile-services-dotnet-backend-service-side-authorization.md
[Didacticiel Démarrage rapide de Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Prise en main des données]: mobile-services-dotnet-backend-ios-get-started-data.md
[Prise en main de l'authentification]: mobile-services-dotnet-backend-ios-get-started-users.md
[Prise en main des notifications Push]: mobile-services-dotnet-backend-ios-get-started-push.md
[Autorisation des utilisateurs avec des scripts]: mobile-services-dotnet-backend-ios-authorize-users-in-scripts.md

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[Inscription du package de votre application Windows Store pour l'authentification Microsoft]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->