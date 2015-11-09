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
	ms.date="08/14/2015" 
	ms.author="glenga"/>

# Ajout de notifications Push à votre application Windows Runtime 8.1 universelle

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Vue d'ensemble

Cette rubrique vous montre comment envoyer des notifications Push à une application Windows Runtime 8.1 universelle à l'aide d’Azure App Service Mobile Apps et d’Azure Notification Hubs. Dans ce scénario, lorsqu'un nouvel élément est ajouté, votre serveur principal d’applications mobiles envoie une notification Push à toutes les applications Windows qui sont inscrites auprès de Windows Notification Service (WNS).

Ce didacticiel est basé sur le démarrage rapide d'App Service Mobile App. Avant de commencer ce didacticiel, vous devez suivre le didacticiel de démarrage rapide [Créer une application Windows](../app-service-mobile-windows-store-dotnet-get-started.md). Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un [compte Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) actif.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Suivez le [didacticiel de démarrage rapide](../app-service-mobile-windows-store-dotnet-get-started.md).  



##<a name="create-gateway"></a>Créer un concentrateur de notification

Procédez comme suit pour créer un concentrateur de notification pour gérer les notifications Push. Si vous disposez déjà d’un concentrateur dans le même groupe de ressources,vous n’avez pas besoin de suivre cette section.

1. Visitez le [portail Azure]. Cliquez sur **Parcourir tout** > **Applications mobiles** > serveur principal que vous venez de créer. Cliquez sur **Paramètres** > **Mobile** > **Push**. 

2. Suivez le flux de travail pour créer un concentrateur de notification. Vous devrez créer un nouvel espace de noms s'il n’en existe aucun dans votre groupe de ressources actuel. Cliquez sur **Créer** une fois que vous avez configuré tous les paramètres.

Vous utilisez ensuite ce concentrateur de notification pour activer les notifications Push pour votre application.

##Inscription de votre application pour les notifications Push

Pour pouvoir envoyer des notifications Push vers des applications Windows à partir d’Azure, vous devez soumettre votre application au Windows Store. Vous pouvez ensuite configurer votre projet de serveur pour l’intégrer à WNS.

1. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d'application Windows Store, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**. 

    ![Associer une application au Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-win8-app.png)
    
2. Dans l'Assistant, cliquez sur **Suivant**, connectez-vous à votre compte Microsoft, tapez un nom pour votre application dans **Réserver un nouveau nom d'application**, puis cliquez sur **Réserver**.

3. Une fois l'inscription de l'application créée, sélectionnez le nouveau nom de l'application, cliquez sur **Suivant**, puis sur **Associer**. Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.

7. Répétez les étapes 1 et 3 pour le projet d'application Windows Phone Store à l'aide de la même inscription que vous avez créée précédemment pour l'application Windows Store.

7. Accédez au [Centre de développement Windows](https://dev.windows.com/fr-FR/overview), connectez-vous à l'aide de votre compte Microsoft, cliquez sur la nouvelle inscription d'application dans **Mes applications**, puis développez **Services** > **Notifications Push**.

8. Sur la page **Notifications Push**, cliquez sur **Site des services Live** sous **Microsoft Azure Mobile Services**.

9. Dans l'onglet **Paramètres de l'application**, notez les valeurs des options **Clé secrète client** et **Identificateur de sécurité (SID) du package**.

    ![Paramètre d’application dans le centre de développement](./media/app-service-mobile-windows-store-dotnet-get-started-push/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT]La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces valeurs avec quiconque et ne les distribuez pas avec votre application.

##Configuration de l'application mobile pour l'envoi de demandes push

1. Connectez-vous au [portail Azure], sélectionnez **Parcourir** > **Application mobile** > votre application > **Services de notification Push**.

2. Dans **Service de notification Windows**, entrez la **clé de sécurité** (clé secrète client) et le **SID du package** que vous avez obtenu à partir du site des services Live, puis cliquez sur **Enregistrer**.

Votre serveur principal d’applications mobiles est maintenant configuré pour fonctionner avec WNS.

##<a id="update-service"></a>Mise à jour du serveur pour l'envoi de notifications Push

Maintenant que les notifications push sont activées dans l'application, vous devez mettre à jour votre backend d'application pour envoyer des notifications push.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur, puis cliquez sur **Gérer les packages NuGet**, recherchez `Microsoft.Azure.NotificationHubs`, puis cliquez sur **Installer**. Cette commande installe la bibliothèque cliente Notification Hubs.

3. Dans le serveur de projet, ouvrez **Contrôleurs** > **TodoItemController.cs** et ajoutez les instructions using suivantes :

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. Dans la méthode **PostTodoItem**, ajoutez le code suivant après l'appel à **InsertAsync** :

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

1. Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis cliquez sur **Manage NuGet Packages**. 

    La boîte de dialogue Gérer les packages NuGet s'affiche.

2. Recherchez le Kit de développement logiciel (SDK) de client App Service Mobile App et cliquez sur **Installer**, sélectionnez tous les projets clients de la solution et acceptez les conditions d’utilisation.

    Cette opération lance le téléchargement, l'installation et l'ajout d'une référence dans tous les projets clients à la bibliothèque Azure Mobile Push pour Windows.

3. Ouvrez le fichier de projet partagé **App.xaml.cs** et ajoutez les instructions `using` suivantes :

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

4. Dans le même fichier, ajoutez la définition de la méthode **InitNotificationsAsync** à la classe **App** :
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Ce code récupère l'URI de canal ChannelURI pour l'application dans WNS et l'inscrit avec votre application App Service Mobile App.
    
5. En haut du gestionnaire d'événements **OnLaunched** dans **App.xaml.cs**, ajoutez le modificateur **async** à la définition de méthode et ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync**, comme dans l’exemple suivant :

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Cela garantit l'inscription de l'URI de canal ChannelURI de courte durée chaque fois que l'application est lancée.

6. Dans l’Explorateur de solutions, double-cliquez sur **Package.appxmanifest** de l’application Windows Store, dans **Notifications**, définissez **Compatible toast** sur **Oui** :

    Dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

7. Répétez l'étape précédente dans le projet d'application Windows Phone Store.

Votre application est maintenant prête à recevoir des notifications toast.

##<a id="test"></a>Tester les notifications push dans votre application

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

<!-- Anchors. -->

<!-- URLs. -->
[portail Azure]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=Nov15_HO1-->