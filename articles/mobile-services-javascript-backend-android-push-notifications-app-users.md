<properties linkid="/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Send push notifications to authenticated users" authors="wesmc" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Envoi de notifications Push aux utilisateurs authentifiés

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="Windows Store&nbsp;C#">Windows Store&nbsp;C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/" title=".NET backend">Serveur principal .NET</a> | 
<a href="/fr-fr/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/"  title="JavaScript backend" class="current">JavaScript backend</a>
</div>

Cette rubrique montre comment envoyer des notifications Push à un utilisateur authentifié sur un appareil inscrit. À l'inverse du précédent didacticiel relatif aux [notifications Push][notifications Push], celui-ci modifie votre service mobile pour exiger l'authentification des utilisateurs avant que le client puisse s'inscrire auprès du concentrateur de notification pour les notifications Push. L'inscription est également modifiée pour ajouter une balise en fonction de l'ID utilisateur assigné. Enfin, le script serveur est mis à jour pour envoyer une notification à l'utilisateur authentifié uniquement plutôt qu'à tous les utilisateurs inscrits.

Ce didacticiel vous familiarise avec les procédures suivantes :

-   [Mise à jour du service pour demander l'authentification pour l'inscription][Mise à jour du service pour demander l'authentification pour l'inscription]
-   [Mise à jour de l'application pour se connecter avant l'inscription][Mise à jour de l'application pour se connecter avant l'inscription]
-   [Test de l'application][Test de l'application]

Ce didacticiel prend en charge les applications Android.

## Configuration requise

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

-   [Prise en main de l'authentification][Prise en main de l'authentification]
    Ajoute une exigence de connexion à l'exemple d'application TodoList.

-   [Prise en main des notifications Push][notifications Push]
    Configure l'exemple d'application TodoList pour les notifications Push à l'aide de Notification Hubs.

Une fois ces deux didacticiels terminés, vous saurez comment empêcher les utilisateurs non authentifiés de s'inscrire pour recevoir les notifications Push de votre service mobile.

## <a name="register"></a>Mise à jour du service pour demander l'authentification pour l'inscription

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app][mobile-services-javascript-backend-register-windows-store-app]]

1.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

        function insert(item, user, request) {

            // Define a payload for the Google Cloud Messaging toast notification.
            var payload = 
                '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

            // Get the ID of the logged-in user.
            var userId = user.userId;       

            request.execute({
                success: function() {
                    // If the insert succeeds, send a notification to all devices 
                    // registered to the logged-in user as a tag.
                    push.gcm.send(userId, payload, {
                        success: function(pushResponse) {
                            console.log("Sent push with " + userId + " tag:", pushResponse, payload);
                            request.respond();
                            },              
                            error: function (pushResponse) {
                                    console.log("Error Sending push:", pushResponse);
                                request.respond(500, { error: pushResponse });
                                }
                            });
                        },
                        error: function(err) {
                            console.log("request.execute error", err)
                            request.respond();
                        }
                    });
        }

    Ce script d'insertion utilise la balise ID utilisateur pour envoyer une notification Push (avec le texte de l'élément inséré) à toutes les inscriptions Google Cloud Messaging créées par l'utilisateur connecté.

## <a name="update-app"></a>Mise à jour de l'application pour se connecter avant l'inscription

[WACOM.INCLUDE [mobile-services-android-push-notifications-app-users][mobile-services-android-push-notifications-app-users]]

## <a name="test"></a>Test de l'application

[WACOM.INCLUDE [mobile-services-android-test-push-users][mobile-services-android-test-push-users]]

<!---## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->
<!-- Anchors. -->
<!-- URLs. -->

  [Windows Store C\#]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users "Android"
  [Serveur principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/ "JavaScript backend"
  [notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Mise à jour du service pour demander l'authentification pour l'inscription]: #register
  [Mise à jour de l'application pour se connecter avant l'inscription]: #update-app
  [Test de l'application]: #test
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-android-get-started-users/
  [mobile-services-javascript-backend-register-windows-store-app]: ../includes/mobile-services-javascript-backend-push-notifications-app-users.md
  [mobile-services-android-push-notifications-app-users]: ../includes/mobile-services-android-push-notifications-app-users.md
  [mobile-services-android-test-push-users]: ../includes/mobile-services-android-test-push-users.md
