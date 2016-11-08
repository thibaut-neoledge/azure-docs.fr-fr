---
title: Envoyer des notifications Push aux utilisateurs authentifiés dans iOS (backend JavaScript)
description: Découvrez comment envoyer des notifications Push à certains utilisateurs
services: mobile-services,notification-hubs
documentationcenter: ios
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: krisragh

---
# Envoi de notifications Push aux utilisateurs authentifiés
[!INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Pour la version Mobile Apps équivalente de cette rubrique, consultez [How to: Send push notifications to an authenticated user using tags](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#push-user).
> 
> 

Dans cette rubrique, vous découvrez comment envoyer des notifications Push à un utilisateur authentifié sur iOS. Avant de commencer ce didacticiel, vous devez effectuer les didacticiels [Prise en main de l'authentification] et [Prise en main des notifications Push].

Dans ce didacticiel, les utilisateurs s'authentifient, puis s'inscrivent auprès du hub de notification pour les notifications Push, et les scripts serveur sont finalement mis à jour pour envoyer ces notifications à des utilisateurs authentifiés uniquement.

## <a name="register"></a>Mise à jour du service pour demander l'authentification pour l'inscription
[!INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

Remplacez la fonction `insert` par le code suivant, puis cliquez sur **Enregistrer**. Ce script d'insertion utilise la balise d'ID utilisateur pour envoyer une notification Push à toutes les inscriptions d'application iOS à partir de l'utilisateur connecté :

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

## <a name="update-app"></a>Mise à jour de l'application pour se connecter avant l'inscription
[!INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

## <a name="test"></a>Test de l'application
[!INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Prise en main de l'authentification]: mobile-services-ios-get-started-users.md
[Prise en main des notifications Push]: mobile-services-javascript-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md

<!---HONumber=AcomDC_0727_2016-->