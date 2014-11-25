<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Prise en main des notifications Push dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Cette rubrique montre comment utiliser Azure Mobile Services avec un service principal .NET pour envoyer des notifications Push à une application Windows Store. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide des concentrateurs de notification Azure. Une fois la procédure terminée, votre service mobile utilise Notification Hubs pour envoyer une notification Push depuis le service principal .NET chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

> [WACOM.NOTE]Cette rubrique vous montre comment configurer manuellement des notifications Push à l'aide du service de notification Windows (WNS) pour une application Windows Store. Vous pouvez utiliser les outils de Visual Studio 2013 pour configurer automatiquement les mêmes notifications Push dans un projet d'application Windows. Pour plus d'informations, consultez la page [version de l'application universelle Windows][version de l'application universelle Windows] de ce didacticiel.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application avec WNS et configuration de Mobile Services][Inscription de votre application avec WNS et configuration de Mobile Services]
2.  [Mise à jour de l'application pour l'inscription aux notifications][Mise à jour de l'application pour l'inscription aux notifications]
3.  [Mise à jour du serveur pour l'envoi de notifications Push][Mise à jour du serveur pour l'envoi de notifications Push]
4.  [Activation des notifications push pour les tests en local][Activation des notifications push pour les tests en local]
5.  [Insertion de données pour recevoir des notifications Push][Insertion de données pour recevoir des notifications Push]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données] pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l’Assistant Ajouter une notification Push crée cette connexion pour vous.

## <span id="register"></span></a> Inscription de votre application avec WNS et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Votre service mobile et votre application sont désormais configurés pour fonctionner avec WNS et Notification Hubs. Vous allez ensuite mettre à jour votre application Windows Store afin de l'inscrire aux notifications.

## <span id="update-app"></span></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1.  Ouvrez le fichier default.js et insérez le code suivant après le code permettant de créer l'instance **MobileServiceClient**. Ce code permet de créer un canal de notification Push et de procéder à l'inscription aux notifications Push :

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);
            }, function (error) {
                var message = "Registration failed: " + error.message;
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.showAsync();
            });

    Ce code récupère l'URI de canal ChannelURI pour l'application depuis WNS et l'inscrit aux notifications Push. En cas d'échec de l'inscription, le message d'erreur s'affiche dans une boîte de dialogue.

2.  Dans Visual Studio, ouvrez le fichier Package.appxmanifest et vérifiez que **Compatible toast** est défini sur **Oui** dans l'onglet **Interface utilisateur de l'application**.

    ![][0]

    Cela permet de s'assurer que votre application peut déclencher des notifications toast. Ces notifications sont déjà activées dans le projet de démarrage rapide téléchargé.

## <span id="update-server"></span></a>Mise à jour du serveur pour l'envoi de notifications push

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a>Activation des notifications push pour les tests en local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a> Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Envisagez ensuite de suivre l'un des didacticiels suivants :

-   [Envoyer des notifications Push aux utilisateurs authentifiés][Envoyer des notifications Push aux utilisateurs authentifiés]
     : découvrez comment utiliser les balises pour envoyer des notifications Push à partir d'un service mobile uniquement à un utilisateur authentifié.

Pour plus d'informations sur les rubriques Mobile Services et Notifications Hubs suivantes :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][Présentation de Notification Hubs]

    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Guide de fonctionnement Mobile Services HTML/JavaScript][Guide de fonctionnement Mobile Services HTML/JavaScript]
     
    Découvrez plus en détail comment utiliser Mobile Services avec les applications JavaScript.

  [version de l'application universelle Windows]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push
  [Inscription de votre application avec WNS et configuration de Mobile Services]: #register
  [Mise à jour de l'application pour l'inscription aux notifications]: #update-app
  [Mise à jour du serveur pour l'envoi de notifications Push]: #update-server
  [Activation des notifications push pour les tests en local]: #local-testing
  [Insertion de données pour recevoir des notifications Push]: #test
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
  [Envoyer des notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Guide de fonctionnement Mobile Services HTML/JavaScript]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library
