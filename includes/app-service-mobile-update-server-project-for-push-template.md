
1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur, puis cliquez sur **Gérer les packages NuGet**, recherchez `Microsoft.Azure.NotificationHubs` et enfin sur **Installer**. Cette opération installe la bibliothèque Notification Hubs pour l’envoi de notifications à partir de votre serveur principal.

3. Dans le projet de serveur, ouvrez **Contrôleurs** > **TodoItemController.cs** et ajoutez les instructions Using suivantes :

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. Dans la méthode **PostTodoItem**, ajoutez le code suivant après l'appel d'**InsertAsync** :

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

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Ce code indique au hub de notification d'envoyer une notification modèle à toutes les inscriptions de modèle qui contiennent « messageParam ». La chaîne est insérée à la place de messageParam dans chaque PNS qui comporte une inscription à l'aide de « messageParam ». Cela vous permet d'envoyer la notification à APNS, GCM, WNS ou tout autre PNS.

<!---HONumber=AcomDC_1125_2015-->