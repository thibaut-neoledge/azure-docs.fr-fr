



Lorsque vous envoyez des notifications de modèle, vous devez uniquement fournir un ensemble de propriétés. Dans notre cas, nous enverrons l’ensemble des propriétés contenant la version localisée des actualités, par exemple :

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


Cette section explique comment envoyer des notification à l’aide de l’application console

Le code inclus diffuse vers les appareils iOS et Windows Store, car le serveur principal peut diffuser vers n’importe quel appareil pris en charge.

### <a name="to-send-notifications-using-a-c-console-app"></a>Envoi de notifications à l’aide d’une application de console C#
Modifiez la méthode `SendTemplateNotificationAsync` dans l’application console que vous avez créée précédemment avec le code suivant. Remarquez comment, dans ce cas, il n’est pas obligatoire d’envoyer plusieurs notifications pour différents paramètres régionaux et différentes plateformes.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

                    // Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


Notez que ce simple appel remettra l’information localisée à **tous** vos appareils, indépendamment de leur plateforme, à mesure que votre Notification Hub crée et remet la charge utile native qui convient à l’ensemble des appareils abonnés à une balise spécifique.

### <a name="sending-the-notification-with-mobile-services"></a>Envoi de la notification avec Mobile Services
Dans votre scheduler Mobile Service, vous pouvez utiliser le script suivant :

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });




<!--HONumber=Nov16_HO3-->


