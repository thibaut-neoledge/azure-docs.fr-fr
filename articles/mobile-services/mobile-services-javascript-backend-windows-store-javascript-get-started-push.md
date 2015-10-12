<properties
	pageTitle="Ajout de notifications Push à votre application Mobile Services (Windows Store) | Microsoft Azure"
	description="Découvrez comment utiliser Azure Mobile Services et Notification Hubs pour envoyer des notifications Push à votre application Windows Store."
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="windows"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="glenga"/>


# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../../includes/mobile-services-selector-get-started-push-legacy.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Windows Store. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide des concentrateurs de notification Azure. Une fois la procédure terminée, votre service mobile envoie une notification Push à l'aide des hubs de notification chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

>[AZURE.NOTE]Cette rubrique vous présente la configuration manuelle des notifications Push à l'aide de Windows Notification Service (WNS) pour une application Windows Store. Vous pouvez utiliser les outils Visual Studio 2013 pour configurer automatiquement les mêmes notifications Push dans un projet d'application Windows. Pour plus d'informations, consultez la page [version application Windows universelle](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md) de ce didacticiel.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Inscription de votre application avec WNS et configuration de Mobile Services](#register)
2. [Mise à jour de l'application pour l'inscription aux notifications](#update-app)
3. [Mise à jour des scripts serveur pour l'envoi de notifications Push](#update-scripts)
3. [Insertion de données pour recevoir des notifications Push](#test)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données] pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l’Assistant Ajouter une notification Push crée cette connexion pour vous.

##<a id="register"></a> Inscription de votre application avec WNS et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Votre service mobile et votre application sont désormais configurés pour fonctionner avec WNS et Notification Hubs. Vous allez ensuite mettre à jour votre application Windows Store afin de l'inscrire aux notifications.

##<a id="update-app"></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1. Ouvrez le fichier default.js et insérez le code suivant après le code permettant de créer l'instance **MobileServiceClient** :

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);
            });

	Ce code récupère l'URI de canal ChannelURI pour l'application depuis WNS et l'inscrit aux notifications Push.

2. Appuyez sur **F5** pour lancer l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.

6. Ouvrez le fichier Package.appxmanifest et vérifiez sous l'onglet **Interface utilisateur de l'application** que la valeur définie de **Compatible toast** est bien **Oui**.

   	![][2]

   	Cela permet de s'assurer que votre application peut déclencher des notifications toast.

##<a id="update-scripts"></a> Mise à jour des scripts serveur pour l'envoi de notifications Push

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Test des notifications Push dans votre application

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

<!---+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données] <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de services mobiles.

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

* [Présentation de Notification Hubs] <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Instructions de dépannage et de débogage des solutions Notification Hubs.

* [Guide de fonctionnement Mobile Services .NET] <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.

* [Référence de script serveur Mobile Services] <br/>En savoir plus sur l'implémentation de logique métier dans votre service mobile.

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: ../mobile-services-windows-store-get-started.md
[Prise en main des données]: mobile-services-windows-store-javascript-get-started-data.md
[Prise en main de l'authentification]: mobile-services-windows-store-javascript-get-started-users.md

[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Guide de fonctionnement Mobile Services .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md


[Envoi de notifications Push aux utilisateurs authentifiés]: mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users.md

[Présentation de Notification Hubs]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-windows-store-javascript-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-windows-store-javascript-send-localized-breaking-news.md

<!---HONumber=Oct15_HO1-->