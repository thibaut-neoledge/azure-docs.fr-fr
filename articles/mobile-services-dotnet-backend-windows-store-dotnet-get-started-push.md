<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal" />

# Prise en main des notifications Push dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Cette rubrique montre comment utiliser Azure Mobile Services avec un service principal .NET pour envoyer des notifications Push à une application Windows universelle. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide Windows universel à l'aide d'Azure Notification Hubs. Une fois la procédure terminée, votre service mobile utilise Notification Hubs pour envoyer une notification Push depuis le service principal .NET chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

> [WACOM.NOTE]Cette rubrique vous montre comment configurer manuellement des notifications Push à l'aide du service de notification Windows (WNS) pour une application Windows Store. Vous pouvez utiliser les outils de Visual Studio 2013 pour configurer automatiquement les mêmes notifications Push dans un projet d'application Windows. Pour plus d'informations, consultez la page [version de l'application universelle Windows][version de l'application universelle Windows] de ce didacticiel.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application avec WNS et configuration de Mobile Services][Inscription de votre application avec WNS et configuration de Mobile Services]
2.  [Mise à jour de l'application pour l'inscription aux notifications][Mise à jour de l'application pour l'inscription aux notifications]
3.  [Mise à jour du serveur pour l'envoi de notifications Push][Mise à jour du serveur pour l'envoi de notifications Push]
4.  [Activation des notifications push pour les tests en local][Activation des notifications push pour les tests en local]
5.  [Insertion de données pour recevoir des notifications Push][Insertion de données pour recevoir des notifications Push]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données] pour connecter votre projet au service mobile.

> [WACOM.NOTE]Cette rubrique est compatible avec les applications Windows Phone 8.1. Pour ajouter des notifications Push à une application Windows Phone 8 ou Windows Phone Silverlight 8.1, consultez la page [Prise en main des notifications Push dans Mobile Services][Prise en main des notifications Push dans Mobile Services].

## <span id="register"></span></a> Inscription de votre application avec WNS et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Votre service mobile et vos applications sont désormais configurés pour fonctionner avec WNS et Notification Hubs. Vous allez ensuite mettre à jour votre application Windows universelle afin de l'inscrire aux notifications.

## <span id="update-app"></span></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1.  Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante :

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Ajoutez la méthode `InitNotificationAsync` suivante à la classe **App** pour créer un canal de notification Push et procéder à l'inscription aux notifications Push :

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Ce code récupère l'URI de canal ChannelURI pour l'application depuis WNS et l'inscrit aux notifications Push.

3.  En haut du gestionnaire d'événements **OnLaunched** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync** :

        InitNotificationsAsync();

    Cela permet de s'assurer que l'inscription est demandée à chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude.

4.  Dans Visual Studio, ouvrez le fichier Package.appxmanifest et vérifiez que **Compatible toast** est défini sur **Oui** dans l'onglet **Interface utilisateur de l'application**. Enregistrez le fichier.

    ![][0]

    Cela permet de s'assurer que votre application peut déclencher des notifications toast.

## <span id="update-server"></span></a>Mise à jour du serveur pour l'envoi de notifications push

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a>Activation des notifications push pour les tests en local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a> Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Envisagez ensuite de suivre le didacticiel suivant, [Envoyer des notifications Push aux utilisateurs authentifiés][Envoyer des notifications Push aux utilisateurs authentifiés], qui montre comment utiliser les balises pour envoyer des notifications Push à partir d'un service mobile uniquement à un utilisateur authentifié.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][Présentation de Notification Hubs]

    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Guide de fonctionnement Mobile Services .NET][Guide de fonctionnement Mobile Services .NET]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [version de l'application universelle Windows]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Inscription de votre application avec WNS et configuration de Mobile Services]: #register
  [Mise à jour de l'application pour l'inscription aux notifications]: #update-app
  [Mise à jour du serveur pour l'envoi de notifications Push]: #update-server
  [Activation des notifications push pour les tests en local]: #local-testing
  [Insertion de données pour recevoir des notifications Push]: #test
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Prise en main des notifications Push dans Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Envoyer des notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library
