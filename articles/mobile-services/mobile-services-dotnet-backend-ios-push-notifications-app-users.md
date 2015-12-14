<properties
	pageTitle="Envoi de notifications Push aux utilisateurs authentifiés (backend .NET)"
	description="Découvrez comment envoyer des notifications Push à"
	services="mobile-services,notification-hubs"
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
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Envoi de notifications Push aux utilisateurs authentifiés

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

Dans cette rubrique, vous découvrez comment envoyer des notifications Push à un utilisateur authentifié sur iOS. Avant de commencer ce didacticiel, vous devez effectuer les didacticiels [Prise en main de l'authentification] et [Prise en main des notifications Push].

Dans ce didacticiel, les utilisateurs s'authentifient, puis s'inscrivent auprès du concentrateur de notification pour les notifications Push, et les scripts serveur sont finalement mis à jour pour envoyer ces notifications à des utilisateurs authentifiés uniquement.

##<a name="register"></a>Mise à jour du service pour demander l'authentification pour l'inscription

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Mise à jour de l'application pour se connecter avant l'inscription

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Test de l'application

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Prise en main de l'authentification]: mobile-services-dotnet-backend-ios-get-started-users.md
[Prise en main des notifications Push]: mobile-services-dotnet-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=AcomDC_1203_2015-->