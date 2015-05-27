<properties
	pageTitle="Envoi de notifications Push aux utilisateurs authentifiés"
	description="Découvrez comment envoyer des notifications Push à certains utilisateurs"
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
	ms.date="04/02/2015"
	ms.author="krisragh"/>

# Envoi de notifications Push aux utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

Dans cette rubrique, vous découvrez comment envoyer des notifications Push à un utilisateur authentifié sur iOS. Avant de commencer ce didacticiel, vous devez effectuer les didacticiels [Prise en main de l'authentification] et [Prise en main des notifications Push].

Dans ce didacticiel, les utilisateurs s'authentifient, puis s'inscrivent auprès du concentrateur de notification pour les notifications Push, et les scripts serveur sont finalement mis à jour pour envoyer ces notifications à des utilisateurs authentifiés uniquement.


##<a name="register"></a>Mise à jour du service pour demander l'authentification pour l'inscription

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

Remplacez la fonction `insert` par le code suivant, puis cliquez sur **Enregistrer**. Ce script d'insertion utilise la balise d'ID utilisateur pour envoyer une notification Push à toutes les inscriptions d'application iOS à partir de l'utilisateur connecté :

```
// Get the ID of the logged-in user.
var userId = user.userId; 

function insert(item, user, request) {
    request.execute();
    setTimeout(function() {
        push.apns.send(userId, {
            alert: "Alert: " + item.text,
            payload: {
                "Hey, a new item arrived: '" + item.text + "'"
            }
        });
    }, 2500);
}
```

##<a name="update-app"></a>Mise à jour de l'application pour se connecter avant l'inscription

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Test de l'application

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Prise en main de l'authentification]: mobile-services-ios-get-started-users.md
[Prise en main des notifications Push]: mobile-services-javascript-backend-ios-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md

<!--HONumber=54-->