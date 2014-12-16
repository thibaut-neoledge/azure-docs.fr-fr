<properties pageTitle="Prise en main des notifications Push (Windows Store) | Centre de développement mobile" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc" />


# Ajout de notifications Push à votre application Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Cette rubrique montre comment utiliser Azure Mobile Services avec un service .NET principal pour envoyer des notifications Push à une application du Windows Store. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide des concentrateurs de notification Azure. Une fois la procédure terminée, votre service mobile utilise Notification Hubs pour envoyer une notification Push depuis le service principal .NET chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

>[WACOM.NOTE]Cette rubrique vous montre comment configurer manuellement les notifications Push à l'aide de Windows Notification Service (WNS) pour une application du Windows Store. Vous pouvez utiliser les outils Visual Studio 2013 pour configurer automatiquement les mêmes notifications Push dans un projet d'application Windows. Pour plus d'informations, consultez la [version application Windows universelle](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push) de ce didacticiel.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Inscription de votre application avec WNS et configuration de Mobile Services](#register)
2. [Mise à jour de l'application pour l'inscription aux notifications](#update-app)
3. [Mise à jour du serveur pour l'envoi de notifications Push](#update-server)
4. [Activation des notifications Push pour les tests en local](#local-testing)
5. [Insertion de données pour recevoir des notifications Push](#test)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données] pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l'Assistant Ajouter une notification Push crée cette connexion pour vous. 

##<a id="register"></a> Inscription de votre application avec WNS et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Votre service mobile et votre application sont désormais configurés pour fonctionner avec WNS et Notification Hubs. Vous allez ensuite mettre à jour votre application Windows Store afin de l'inscrire aux notifications.

##<a id="update-app"></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1. Ouvrez le fichier default.js et insérez le code suivant après le code permettant de créer l'instance **MobileServiceClient**. Ce code permet de créer un canal de notification Push et de procéder à l'inscription aux notifications Push :

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

    Ce code récupère l'URI de canal ChannelURI pour l'application depuis WNS et l'inscrit aux notifications Push. En cas d'échec de l'inscription, le message d'erreur s'affiche dans une boîte de message.

2. Dans Visual Studio, ouvrez le fichier Package.appxmanifest et vérifiez que **Compatible toast** a la valeur **Oui** dans l'onglet **Interface utilisateur de l'application**.

   	![][1]

   	Cela permet de s'assurer que votre application peut déclencher des notifications toast. Ces notifications sont déjà activées dans le projet de démarrage rapide téléchargé.

##<a id="update-server"></a> Mise à jour du serveur pour l'envoi de notifications Push


[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Activation des notifications Push pour les tests en local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Nous vous invitons maintenant à suivre l'un des didacticiels suivants :

+ [Envoi de notifications Push aux utilisateurs authentifiés]
	<br/>Apprenez à utiliser des balises pour envoyer des notifications Push à partir d'un service mobile aux utilisateurs authentifiés uniquement.

Pour plus d'informations sur les rubriques Mobile Services et Notifications Hubs suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec différents types de comptes à l'aide des services mobiles.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications de Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtention de conseils de dépannage et débogage des solutions Notification Hubs. 

* [Guide de fonctionnement de Mobile Services avec HTML/JavaScript]
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec des applications JavaScript.

<!-- Anchors. -->

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Page Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users

[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/

[Guide de fonctionnement de Mobile Services avec HTML/JavaScript]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library
