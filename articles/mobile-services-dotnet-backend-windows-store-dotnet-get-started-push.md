<properties 
	pageTitle="Prise en main des concentrateurs de notification Push à l'aide des services mobiles .NET runtime" 
	description="Découvrez comment utiliser Azure Mobile Services et Notification Hubs pour envoyer des notifications Push à votre application Windows Store." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc,ricksal"/>


# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Cette rubrique explique comment utiliser Azure Mobile Services avec un service principal .NET pour envoyer des notifications Push à une application Windows universelle. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide Windows universel à l'aide d'Azure Notification Hubs. Une fois la procédure terminée, votre service mobile utilise Notification Hubs pour envoyer une notification Push depuis le service principal .NET chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

>[AZURE.NOTE]Cette rubrique vous montre comment configurer manuellement les notifications Push à l'aide de Windows Notification Service (WNS) pour une application Windows Store. Vous pouvez utiliser les outils Visual Studio 2013 pour configurer automatiquement les mêmes notifications Push dans un projet d'application Windows. Pour plus d'informations, consultez la [version d'application Windows universelle](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push) de ce didacticiel.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Inscription de votre application avec WNS et configuration de Mobile Services](#register)
2. [Mise à jour de l'application pour l'inscription aux notifications](#update-app)
3. [Mise à jour du serveur pour l'envoi de notifications push](#update-server)
4. [Activation des notifications Push pour les tests en local](#local-testing)
3. [Insertion de données pour recevoir des notifications Push](#test)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données] pour connecter votre projet au service mobile. 

>[AZURE.NOTE]Cette rubrique prend en charge les applications Windows Phone Store 8.1. Pour ajouter des notifications Push à une application Windows Phone 8 ou Windows Phone Silverlight 8.1, consultez cette version de [Prise en main des notifications Push dans Mobile Services](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push).

##<a id="register"></a> Inscription de votre application avec WNS et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Votre service mobile et vos applications sont désormais configurés pour fonctionner avec WNS et Notification Hubs. Vous allez ensuite mettre à jour votre application Windows universelle afin de l'inscrire aux notifications.

##<a id="update-app"></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1. Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez les instructions `using` suivantes :

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Ajoutez la méthode `InitNotificationAsync` suivante à la classe **App** pour créer un canal de notification Push et procéder à l'inscription aux notifications Push : 
	
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
    
3. En haut du gestionnaire d'événements **OnLaunched** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync** :

        InitNotificationsAsync();

	Cela permet de s'assurer que l'inscription est demandée à chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude. 

4. Dans Visual Studio, ouvrez le fichier Package.appxmanifest et vérifiez que **Compatible toast** est défini sur **Oui** sous l'onglet **Interface utilisateur de l'application**. Enregistrez le fichier.

   	![][1]

   	Cela permet de s'assurer que votre application peut déclencher des notifications toast. 

##<a id="update-server"></a> Mise à jour du serveur pour l'envoi de notifications push

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Activation des notifications Push pour les tests en local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Test des notifications Push dans votre application

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

<!--+ [Envoi de notifications Push aux utilisateurs authentifiés]
	<br/>En savoir plus sur l'utilisation de balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

+ [Envoi de notifications diffusées aux abonnés]
	<br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

+ [Envoi de notifications basées sur un modèle aux abonnés]
	<br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.
-->
Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenez des conseils sur le dépannage et le débogage des solutions Notification Hubs. 

* [Guide de fonctionnement Mobile Services .NET]
  <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users

[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
[Envoi de notifications diffusées aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Envoi de notifications basées sur un modèle aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/


[Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library


<!--HONumber=42-->
