
Ensuite, vous devez changer la façon dont les notifications Push sont inscrites pour vous assurer que l'utilisateur est authentifié avant de tenter une inscription. L'application cliente se met à jour selon la façon dont vous avez implémenté les notifications Push.

###Utilisation de l'Assistant Notification Push dans Visual Studio 2013 Update 2 ou ultérieur

Dans cette méthode, l'Assistant a généré de nouveaux fichiers push.register.js et service.js dans votre projet.

>[AZURE.NOTE]L'Assistant Ajout de notification Push est actuellement pris en charge pour un service mobile principal .NET uniquement.

1. Dans Visual Studio, dans l'Explorateur de solutions, ouvrez le fichier de projet push.register.js et placez en commentaire ou supprimez l'appel à **addEventListener**. 

2. Dans le fichier de projet default.js, remplacez la fonction **login** existante par le code suivant :
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

###Notifications Push activées manuellement		

Dans cette méthode, vous avez ajouté du code pour l'inscription provenant du didacticiel directement dans le fichier de projet default.js.

1. Dans Visual Studio, dans l'Explorateur de solutions, ouvrez le fichier de projet default.js et, dans le gestionnaire d'événements **onActivated**, recherchez la ligne de code qui appelle la fonction **createPushNotificationChannelForApplicationAsync** et qui ressemble à ceci :

		// Request a push notification channel.
		Windows.Networking.PushNotifications
		    .PushNotificationChannelManager
		    .createPushNotificationChannelForApplicationAsync()
		    .then(function (channel) {
		        // Register for notifications using the new channel
		        client.push.registerNative(channel.uri);
		    }); 
 
2. Déplacez cette ligne de code dans la fonction **login**, juste avant l'appel à **refreshTodoItems**. La fonction **login** doit alors se présenter comme ceci :
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

<!--HONumber=42-->
