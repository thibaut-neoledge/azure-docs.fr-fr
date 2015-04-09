<properties
	pageTitle="Ajout de l'authentification à une application Azure Mobile Services existante (iOS) | Centre de développement mobile"
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

Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide en faisant appel à un fournisseur d'identité pris en charge.

Ce didacticiel est basé sur le [didacticiel Démarrage rapide de Mobile Services], que vous devez effectuer en premier.

##<a name="register"></a>Inscription de l'application pour l'authentification et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

Dans Xcode, ouvrez le projet. Appuyez sur le bouton **Exécuter** pour démarrer l'application. Vérifiez qu'une exception avec code d'état 401 (non autorisé) est générée après le démarrage de l'application. Cela se produit, car l'application tente d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ exige à présent l'authentification.

##<a name="add-authentication"></a>Ajout de l'authentification à l'application

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Stockage des jetons d'authentification dans l'application

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services], vous utiliserez la valeur d'ID utilisateur pour filtrer les données retournées.

<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
[Ajout de l'authentification à l'application]: #add-authentication
[Étapes suivantes]:#next-steps
[Stockage des jetons d'authentification dans votre application]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Autorisation côté service des utilisateurs Mobile Services]: mobile-services-javascript-backend-service-side-authorization.md
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Authentification unique pour les applications Windows Store à l'aide de Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Didacticiel Démarrage rapide de Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Prise en main des données]: /develop/mobile/tutorials/get-started-with-data-ios
[Prise en main de l'authentification]: /develop/mobile/tutorials/get-started-with-users-ios
[Prise en main des notifications Push]: /develop/mobile/tutorials/get-started-with-push-ios
[Autorisation des utilisateurs avec des scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=49-->