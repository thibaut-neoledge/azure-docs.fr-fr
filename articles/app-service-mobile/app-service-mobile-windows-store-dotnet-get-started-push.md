<properties 
	pageTitle="Ajout de notifications Push à votre application Windows Runtime 8.1 universelle | Azure Mobile Apps" 
	description="Découvrez comment utiliser Azure App Service Mobile Apps et Azure Notification Hubs pour envoyer des notifications Push à votre application Windows." 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2015" 
	ms.author="glenga"/>

# Ajout de notifications Push à votre application Windows Runtime 8.1 universelle

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Vue d'ensemble

Cette rubrique vous montre comment envoyer des notifications Push à une application Windows Runtime 8.1 universelle à l'aide d’Azure App Service Mobile Apps et d’Azure Notification Hubs. Dans ce scénario, lorsqu'un nouvel élément est ajouté, votre serveur principal d’applications mobiles envoie une notification Push à toutes les applications Windows qui sont inscrites auprès de Windows Notification Service (WNS).

Ce didacticiel est basé sur le démarrage rapide d'App Service Mobile App. Avant de commencer ce didacticiel, vous devez suivre le didacticiel de démarrage rapide [Créer une application Windows](../app-service-mobile-windows-store-dotnet-get-started.md). Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un [compte Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) actif.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Suivez le [didacticiel de démarrage rapide](../app-service-mobile-windows-store-dotnet-get-started.md).  


##<a name="create-hub"></a>Créer un concentrateur de notification

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Inscription de votre application pour les notifications Push

Pour pouvoir envoyer des notifications Push vers des applications Windows à partir d’Azure, vous devez soumettre votre application au Windows Store. Vous pouvez ensuite configurer votre projet de serveur pour l’intégrer à WNS.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##Configuration de l'application mobile pour l'envoi de demandes push

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Mise à jour du serveur pour l'envoi de notifications Push

Maintenant que les notifications push sont activées dans l'application, vous devez mettre à jour votre backend d'application pour envoyer des notifications push.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet serveur, puis cliquez sur **Gérer les packages NuGet**, recherchez `Microsoft.Azure.NotificationHubs` et cliquez enfin sur **Installer**. Cette commande installe la bibliothèque cliente Notification Hubs.

2. Dans le projet serveur, ouvrez **Contrôleurs** > **TodoItemController.cs** et ajoutez les instructions Using suivantes :

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. Dans la méthode **PostTodoItem**, ajoutez le code suivant après l'appel à **InsertAsync** :

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Ce code indique au concentrateur de notification d'envoyer une notification Push après l'insertion d’un nouvel élément.


## <a name="publish-the-service"></a>Publication du serveur principal mobile sur Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>Ajout de notifications Push à votre application

1. Ouvrez le fichier projet partagé **App.xaml.cs** et ajoutez les instructions `using` suivantes :

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. Dans le même fichier, ajoutez la définition de la méthode **InitNotificationsAsync** à la classe **App** :
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Ce code récupère l'URI de canal ChannelURI pour l'application dans WNS et l'inscrit avec votre application App Service Mobile App.
    
3. En haut du gestionnaire d'événements **OnLaunched** dans **App.xaml.cs**, ajoutez le modificateur **async** à la définition de méthode et ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync**, comme dans l'exemple suivant :

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Cela garantit l'inscription de l'URI de canal ChannelURI de courte durée chaque fois que l'application est lancée.

4. Dans l’Explorateur de solutions, double-cliquez sur **Package.appxmanifest** de l’application Windows Store, dans **Notifications**, définissez **Compatible toast** sur **Oui** :

    Dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

5. Répétez l'étape précédente dans le projet d'application Windows Phone Store.

Votre application est maintenant prête à recevoir des notifications toast.

##<a id="test"></a>Tester les notifications push dans votre application

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

##<a id="more"></a>En savoir plus

* Les modèles vous apportent la souplesse nécessaire pour envoyer des notifications push multiplateformes et localisées. [Utilisation du client géré pour Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md) vous montre comment enregistrer des modèles.
* Les balises vous permettent de vous permettent de cibler des clients segmentés avec des notifications push. [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) vous montre comment ajouter des balises à l'installation d'un périphérique.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_1125_2015--->