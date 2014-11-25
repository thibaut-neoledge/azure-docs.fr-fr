<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Prise en main des notifications Push dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Windows universelle.
Dans ce didacticiel, vous allez activer les notifications Push utilisant Azure Notification Hubs au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push à l'aide des concentrateurs de notification chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

> [WACOM.NOTE]Cette rubrique vous montre comment configurer manuellement les notifications Push à l'aide de Mobile Services dans un projet Windows Store. Vous pouvez utiliser Visual Studio 2013 pour ajouter les mêmes notifications Push à un projet d'application Windows Store. Pour plus d'informations, consultez la page [version application Windows universelle][version application Windows universelle] de ce didacticiel.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application avec WNS et configuration de Mobile Services][Inscription de votre application avec WNS et configuration de Mobile Services]
2.  [Mise à jour de l'application pour l'inscription aux notifications][Mise à jour de l'application pour l'inscription aux notifications]
3.  [Mise à jour des scripts serveur pour l'envoi de notifications Push][Mise à jour des scripts serveur pour l'envoi de notifications Push]
4.  [Insertion de données pour recevoir des notifications Push][Insertion de données pour recevoir des notifications Push]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données] pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l’Assistant Ajouter une notification Push crée cette connexion pour vous.

> [WACOM.NOTE]Cette rubrique prend en charge les applications Windows Phone Store 8.1. Pour ajouter des notifications Push à une application Windows Phone 8 ou Silverlight pour Windows Phone 8.1, consultez cette version de [Prise en main des notifications Push dans Mobile Services][Prise en main des notifications Push dans Mobile Services].

## <span id="register"></span></a> Inscription de votre application avec WNS et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Votre service mobile et votre application sont désormais configurés pour fonctionner avec WNS et Notification Hubs. Vous allez ensuite mettre à jour votre application Windows Store universelle afin de l'inscrire aux notifications.

## <span id="update-app"></span></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1.  Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez les instructions `using` suivantes :

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Ajoutez la méthode suivante à la classe **App** :

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel =
                await PushNotificationChannelManager
                    .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
        }

    Ce code récupère l'URI de canal ChannelURI pour l'application depuis WNS et l'inscrit aux notifications Push.

3.  En haut du gestionnaire d'événements **OnLaunched** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync** :

        InitNotificationsAsync();

    Cela permet de s'assurer que l'inscription est demandée à chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude.

4.  Appuyez sur **F5** pour lancer l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.

5.  (Facultatif) Si vous n'utilisez pas le projet de démarrage rapide généré par le portail de gestion, ouvrez le fichier Package.appxmanifest et vérifiez sous l'onglet **Interface utilisateur de l'application** que la valeur de **Compatible toast** est définie sur **Oui**.

    ![][0]

    Cela permet de s'assurer que votre application peut déclencher des notifications toast. Ces notifications sont déjà activées dans le projet de démarrage rapide téléchargé.

## <span id="update-scripts"></span></a> Mise à jour des scripts serveur pour l'envoi de notifications Push

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs pour envoyer des notifications Push. Nous vous invitons maintenant à suivre l'un des didacticiels suivants :

-   [Envoi de notifications Push à des utilisateurs authentifiés][Envoi de notifications Push à des utilisateurs authentifiés]

    En savoir plus sur l'utilisation de balises pour envoyer des notifications Push depuis un service mobile uniquement à un utilisateur authentifié.

-   [Envoi de notifications diffusées aux abonnés][Envoi de notifications diffusées aux abonnés]

    En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

-   [Envoi de notifications basées sur un modèle aux abonnés][Envoi de notifications basées sur un modèle aux abonnés]

    En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][Présentation de Notification Hubs]

    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Guide de fonctionnement Mobile Services .NET][Guide de fonctionnement Mobile Services .NET]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

-   [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services]

    En savoir plus sur l'implémentation de logique métier dans votre service mobile.

  [version application Windows universelle]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Inscription de votre application avec WNS et configuration de Mobile Services]: #register
  [Mise à jour de l'application pour l'inscription aux notifications]: #update-app
  [Mise à jour des scripts serveur pour l'envoi de notifications Push]: #update-scripts
  [Insertion de données pour recevoir des notifications Push]: #test
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Prise en main des notifications Push dans Mobile Services]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [Envoi de notifications Push à des utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [Envoi de notifications diffusées aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
  [Envoi de notifications basées sur un modèle aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
