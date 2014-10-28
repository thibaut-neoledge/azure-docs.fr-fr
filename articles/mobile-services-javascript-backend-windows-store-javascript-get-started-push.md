<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"></tags>

# Prise en main des notifications Push dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][]]

Cette rubrique explique comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Windows Store.
Dans ce didacticiel, vous activez les notifications Push à l'aide de Azure Notification Hubs vers le projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push à l'aide des concentrateurs de notification chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

> [WACOM.NOTE]Cette rubrique vous montre comment configurer manuellement les notifications Push à l'aide de Windows Notification Service (WNS) pour une application Windows Store. Vous pouvez utiliser les outils Visual Studio 2013 pour configurer automatiquement les mêmes notifications Push dans un projet d'application Windows. Pour plus d'informations, consultez la page [version application Windows universelle][] de ce didacticiel.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application avec WNS et configuration de Mobile Services][]
2.  [Mise à jour de l'application pour l'inscription aux notifications][]
3.  [Mise à jour des scripts serveur pour l'envoi de notifications Push][]
4.  [Insertion de données pour recevoir des notifications Push][]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services][] ou [Prise en main des données][] pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l’Assistant Ajouter une notification Push crée cette connexion pour vous.

## <span id="register"></span></a> Inscription de votre application avec WNS et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app][]]

Votre service mobile et votre application sont désormais configurés pour fonctionner avec WNS et Notification Hubs. Vous allez ensuite mettre à jour votre application Windows Store afin de l'inscrire aux notifications.

## <span id="update-app"></span></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1.  Ouvrez le fichier default.js et insérez le code suivant après le code permettant de créer l'instance **MobileServiceClient** :

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);                    
            });      

    Ce code récupère l'URI de canal ChannelURI pour l'application depuis WNS et l'inscrit aux notifications Push.

2.  Appuyez sur **F5** pour lancer l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.

3.  Ouvrez le fichier Package.appxmanifest et vérifiez sous l'onglet **Interface utilisateur de l'application** que la valeur définie de **Compatible toast** est bien **Oui**.

    ![][]

    Cela permet de s'assurer que votre application peut déclencher des notifications toast.

## <span id="update-scripts"></span></a> Mise à jour des scripts serveur pour l'envoi de notifications Push

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs][]]

## <span id="test"></span></a>Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-windows-store-test-push][]]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs pour envoyer des notifications Push. À présent, nous vous invitons à suivre le didacticiel suivant, [Envoi de notifications Push à des utilisateurs authentifiés][], qui présente l'utilisation de balises pour envoyer des notifications Push depuis un Mobile Service uniquement à un utilisateur authentifié.

<!---+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

-   [Prise en main des données][]

    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][]

    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Guide de fonctionnement Mobile Services .NET][]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'implémentation de logique métier dans votre service mobile.

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [version application Windows universelle]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push
  [Inscription de votre application avec WNS et configuration de Mobile Services]: #register
  [Mise à jour de l'application pour l'inscription aux notifications]: #update-app
  [Mise à jour des scripts serveur pour l'envoi de notifications Push]: #update-scripts
  [Insertion de données pour recevoir des notifications Push]: #test
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  []: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Envoi de notifications Push à des utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
