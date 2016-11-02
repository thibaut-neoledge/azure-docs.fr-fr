Dans cette section, vous mettez à jour le code dans votre projet backend Mobile Apps existant pour envoyer une notification push chaque fois qu’un nouvel élément est ajouté. Étant donné que les clients sont inscrits pour les notifications push à l’aide d’un modèle d’inscription, un même message de notification push peut être envoyé à toutes les plateformes clientes. Chaque inscription de modèle de client contient un paramètre *messageParam*. Lorsque la notification est envoyée, *messageParam* contient une chaîne qui représente le texte de l’élément inséré. Pour plus d’informations sur l’utilisation de modèles avec Notification Hubs, consultez [Modèles](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

Choisissez la procédure ci-dessous correspondant à votre type de projet backend : [backend .NET](#dotnet) ou [backend Node.js](#nodejs).

### <a name="dotnet"></a>Projet de serveur principal .NET
1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur, puis cliquez sur **Gérer les packages NuGet**, recherchez `Microsoft.Azure.NotificationHubs` et cliquez sur **Installer**. Cette opération installe la bibliothèque Notification Hubs pour l’envoi de notifications à partir de votre serveur principal.

3. Dans le projet de serveur, ouvrez **Contrôleurs** > **TodoItemController.cs** et ajoutez les instructions using suivantes :

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. Dans la méthode **PostTodoItem**, ajoutez le code suivant après l’appel d’**InsertAsync** :

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

	Une notification de modèle contenant item.Text est ensuite envoyée quand un nouvel élément est inséré.

4. Publier à nouveau le projet de serveur

### <a name="nodejs"></a>Projet de back-end Node.js

1. Si vous ne l’avez pas déjà fait, [téléchargez le projet backend de démarrage rapide](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou utilisez l’[éditeur en ligne du portail Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Remplacez le code présent dans todoitem.js par le code suivant :

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');
	
		var table = azureMobileApps.table();
		
		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK, 
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');
	    
	    // Define the template payload.
	    var payload = '{"messageParam": "' + context.item.text + '" }';  
	    
	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a template notification.
	                context.push.send(null, payload, function (error) {
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

	Une notification de modèle contenant item.text est ensuite envoyée quand un nouvel élément est inséré.

2. Quand vous modifiez le fichier sur votre ordinateur local, republiez le projet serveur.

<!---HONumber=AcomDC_0629_2016-->