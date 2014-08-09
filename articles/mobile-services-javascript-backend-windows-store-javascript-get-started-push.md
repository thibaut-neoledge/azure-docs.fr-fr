<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main des notifications Push dans Mobile Services
=========================================================

[Windows Store C\#](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone")[iOS](/fr-fr/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android")
[.NET backend](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ ".NET backend") | [JavaScript backend](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/ "JavaScript backend")

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Windows Store. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide des concentrateurs de notification Azure. Une fois la procédure terminée, votre service mobile envoie une notification Push à l'aide des concentrateurs de notification chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

> [WACOM.NOTE] Ce didacticiel présente l'intégration de Mobile Services à Notification Hubs, qui en est actuellement au stade préliminaire. Par défaut, l'envoi de notifications Push à l'aide de Notification Hubs n'est pas activé depuis un service principal JavaScript. Une fois le nouveau concentrateur de notification créé, le processus d'intégration est irréversible. Pour le moment, les notifications Push pour iOS et Android sont uniquement disponibles via la prise en charge Push par défaut décrite dans [cette version de la rubrique](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push/).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application avec WNS et configuration de Mobile Services](#register)
2.  [Mise à jour de l'application pour l'inscription aux notifications](#update-app)
3.  [Mise à jour des scripts serveur pour l'envoi de notifications Push](#update-scripts)
4.  [Insertion de données pour recevoir des notifications Push](#test)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services](/fr-fr/documentation/articles/mobile-services-windows-store-get-started) ou [Prise en main des données](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data) pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l'Assistant Ajouter une notification Push crée cette connexion pour vous.

Inscription de votre application avec WNS et configuration de Mobile Services
-----------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app](../includes/mobile-services-javascript-backend-register-windows-store-app.md)]

Votre service mobile et votre application sont désormais configurés pour fonctionner avec WNS et Notification Hubs. Vous allez ensuite mettre à jour votre application Windows Store afin de l'inscrire aux notifications.

Mise à jour de l'application pour l'inscription aux notifications
-----------------------------------------------------------------

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1.  Ouvrez le fichier default.js et insérez le code suivant après le code permettant de créer l'instance **MobileServiceClient** :

         // Demandez un canal de notification Push.
         Windows.Networking.PushNotifications
             .PushNotificationChannelManager
             .createPushNotificationChannelForApplicationAsync()
             .then(function (channel) {
                 // Enregistrez-vous pour les  notifications à l'aide du nouveau canal.
                 client.push.registerNative(channel.uri);                    
             });      

          Ce code récupère l'URI de canal ChannelURI pour l'application depuis WNS et l'enregistre pour les notifications Push.

2.  Appuyez sur la touche **F5** pour exécuter l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.

3.  Ouvrez le fichier Package.appxmanifest et vérifiez sous l'onglet **Interface utilisateur de l'application** que la valeur définie de **Compatible toast** est bien **Oui**.

	![][2]

	Cela vous permet de vous assurer que votre application peut déclencher des notifications toast. 

Mise à jour des scripts serveur pour l'envoi de notifications Push
------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

Test des notifications Push dans votre application
--------------------------------------------------

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

	![][13]

	Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de WNS.

	![][14]

Étapes suivantes
----------------

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
	<br/>En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

-   [Envoi de notifications aux abonnés](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
	<br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

-   [Envoi de notifications aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users/)
	<br/>En savoir plus sur l'envoi de notifications Push depuis un service mobile à certains utilisateurs sur n'importe quel appareil.

-   [Envoi de notifications interplateforme aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
	<br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data)
	<br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users)
	<br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
	<br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services .NET](/fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library)
	<br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.

<!-- Anchors. -->

<!-- Images. -->


[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png
[2]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started
[Get started with data]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data
[Get started with authentication]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users
[Get started with push notifications]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push

[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library