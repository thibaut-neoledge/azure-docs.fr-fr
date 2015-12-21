Utilisez la procédure correspondant à votre type de projet de serveur principal : [serveur principal .NET](#dotnet) ou [serveur principal Node.js](#nodejs).

### <a name="dotnet"></a>Projet de serveur principal .NET

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur, puis cliquez sur **Gérer les packages NuGet**, recherchez `Microsoft.Azure.NotificationHubs` et cliquez sur **Installer**. Cette commande installe la bibliothèque cliente Notification Hubs.

2. Dans le dossier Contrôleurs, ouvrez TodoItemController.cs et ajoutez les instructions `using` suivantes :

        using Microsoft.Azure.Mobile.Server.Config;
        using Microsoft.Azure.NotificationHubs;

3. Remplacez la méthode `PostTodoItem` par le code suivant :

      
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
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

            // Android payload
            var androidNotificationPayload = "{ "data" : {"message":"" + item.Text + ""}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

                // Write the success result to the logs.
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                // Write the failure result to the logs.
                config.Services.GetTraceWriter()
                    .Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

### <a name="nodejs"></a>Projet de serveur principal Node.js

1. Si vous ne l’avez pas déjà fait, [téléchargez le projet de démarrage rapide](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou utilisez l’[éditeur en ligne dans le portail Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
 
1. Remplacez le code présent dans le fichier todoitem.js par le code suivant :

		var azureMobileApps = require('azure-mobile-apps'),
		promises = require('azure-mobile-apps/src/utilities/promises'),
		logger = require('azure-mobile-apps/src/logger');
		
		var table = azureMobileApps.table();
		
		table.insert(function (context) {
		// For more information about the Notification Hubs JavaScript SDK, 
		// see http://aka.ms/nodejshubs
		logger.info('Running TodoItem.insert');
		
		// Define the GCM payload.
		var payload = {
		    "data": {
		        "message": context.item.text
		    }
		};   
		
		// Execute the insert.  The insert returns the results as a Promise,
		// Do the push as a post-execute action within the promise flow.
		return context.execute()
		    .then(function (results) {
		        // Only do the push if configured
		        if (context.push) {
					// Send a GCM native notification.
		            context.push.gcm.send(null, payload, function (error) {
		                if (error) {
		                    logger.error('Error while sending push notification: ', error);
		                } else {
		                    logger.info('Push notification sent successfully!');
		                }
		            });
		        }
		        // Don't forget to return the results from the context.execute()
		        return results;
		    })
		    .catch(function (error) {
		        logger.error('Error while running context.execute: ', error);
		    });
		});
		
		module.exports = table;  

	Ce code envoie une notification GCM contenant item.text quand un nouvel élément todo est inséré.

2. Quand vous modifiez le fichier sur votre ordinateur local, republiez le projet serveur.

<!---HONumber=AcomDC_1210_2015-->