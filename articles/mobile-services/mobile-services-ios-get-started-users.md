<properties
	pageTitle="Ajouter l’authentification à une application Azure Mobile Services existante (iOS) | Backend JavaScript | Microsoft Azure"
	description="Apprenez à utiliser Mobile Services pour authentifier les utilisateurs de votre application iOS par l'intermédiaire de divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="krisragh"/>

# Ajout de l’authentification à une application existante

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Dans ce didacticiel, vous allez ajouter l’authentification au [didacticiel Démarrage rapide de Mobile Services] en faisant appel à un fournisseur d’identité pris en charge.

Nous vous recommandons de terminer d'abord le [didacticiel Démarrage rapide de Mobile Services]. Vous pouvez également télécharger le projet Démarrage rapide iOS : dans le [portail Azure Classic] > **Mobile Services** > votre service mobile > l'authentification cloud en haut à gauche > **iOS** > **Créer une nouvelle application iOS** > **Télécharger et exécuter votre application** > **Objective-C** > **Télécharger**. N'oubliez pas de cliquer sur **Créer une table TodoItem** avant de cliquer sur **Télécharger** si vous n'avez pas encore créé la table.

##<a name="register"></a>Inscription de l’application pour l’authentification

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restriction des autorisations de données aux utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

##<a name="add-authentication"></a>Ajout de l’authentification à l’application

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Stockage des jetons d’authentification dans l’application

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Étapes suivantes

Ensuite, découvrez [comment utiliser la valeur d’ID d’utilisateur pour filtrer les données renvoyées](mobile-services-javascript-backend-service-side-authorization.md).

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Service-side authorization of Mobile Services users]: mobile-services-javascript-backend-service-side-authorization.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[didacticiel Démarrage rapide de Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[portail Azure Classic]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0114_2016-->