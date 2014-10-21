Cette section montre comment envoyer des notifications à partir d'une application console .NET ou autre.
Si vous utilisez Mobile Services, reportez-vous aux didacticiels [Prise en main des notifications push][]. Si vous souhaitez utiliser Java ou PHP, consultez la rubrique [Utilisation de Notification Hubs depuis Java/PHP][]. Vous pouvez envoyer des notifications à partir d’un serveur principal à l’aide de l’[interface REST des Notification Hubs][].

Le code suivant envoie des notifications aux appareils Windows Store, Windows Phone, iOS et Android.

Ignorez les étapes 1 à 3 si vous avez créé une application de console lorsque vous avez effectué la [prise en main des Notification Hubs][].

1.  Dans Visual Studio, créez une application de console Visual C# :

   	![][13]

2.  Dans le menu principal de Visual Studio, cliquez sur **Outils**, **Library Package Manager** et **Console du Gestionnaire de package**, puis dans la fenêtre de la console, tapez la ligne suivante et appuyez sur **Entrée** :

        Install-Package WindowsAzure.ServiceBus

    Une référence au Kit de développement logiciel (SDK) Azure Service Bus est ajoutée à l’aide du [package NuGet WindowsAzure.ServiceBus][].

3.  Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.ServiceBus.Notifications;

4.  Dans la classe `Program`, ajoutez la méthode suivante ou remplacez-la si elle existe déjà :

        private static async void SendNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
                "Technology", "Science", "Sports"};

            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

                    // Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
         }

    Ce code envoie des notifications à chacune des six balises du tableau de chaînes aux appareils iOS, Windows Store et Windows Phone. L’utilisation des balises permet d’envoyer les notifications uniquement aux appareils des catégories inscrites.

    <div class="dev-callout"><strong>Remarque</strong> 
    <p>Ce code principal prend en charge les clients Windows Store, Windows Phone, iOS et Android. Les m&eacute;thodes d&rsquo;envoi retournent une r&eacute;ponse d&rsquo;erreur lorsque le Notification Hub n&rsquo;a pas encore &eacute;t&eacute; configur&eacute; pour une plateforme cliente particuli&egrave;re. </p>
</div>

5.  Dans le code ci-dessus, remplacez les espaces réservés `<hub name>` et `<connection string with full access>` par le nom de votre concentrateur de notification et par la chaîne de connexion de la signature *DefaultFullSharedAccessSignature* obtenue précédemment.

6.  Ajoutez les lignes suivantes à la méthode **Main** :

         SendNotificationAsync();
         Console.ReadLine();

<!-- Anchors --> <!-- Images. --> <!-- URLs. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png
  [Prise en main des notifications push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Utilisation de Notification Hubs depuis Java/PHP]: /fr-fr/documentation/articles/notification-hubs-java-backend-how-to/
  [interface REST des Notification Hubs]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
  [prise en main des Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  
  [package NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
