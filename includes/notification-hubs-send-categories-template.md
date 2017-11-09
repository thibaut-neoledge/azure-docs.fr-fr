
Dans cette section, vous envoyez les dernières nouvelles sous forme de notifications modèles avec balises à partir de l’application console .NET.

Si vous utilisez la fonctionnalité Mobile Apps du service App Service de Microsoft Azure, reportez-vous au didacticiel [Add push notifications for Mobile Apps] (Ajout de notifications push pour Mobile Apps), en choisissant la plateforme en haut.

Si vous souhaitez utiliser Java ou PHP, consultez la rubrique [Utilisation de Notification Hubs à partir de Java ou PHP]. Vous pouvez envoyer des notifications à partir d’un serveur principal à l’aide de l’[interface REST des Notification Hubs].

Ignorez les étapes 1 à 3 si vous avez créé l’application de console pour l’envoi des notifications lorsque vous avez effectué la [prise en main des Notification Hubs].

1. Dans Visual Studio, créez une application de console Visual C# :
   
      ![Lien de l’application console][13]

2. Dans le menu principal de Visual Studio, sélectionnez **Outils** > **Gestionnaire de package de bibliothèques** > **Console du gestionnaire de package**, puis dans la fenêtre de la console, tapez la ligne suivante :
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Sélectionnez **Enter** (Entrer).  
    Cette action ajoute une référence au Kit de développement logiciel (SDK) Azure Notification Hubs à l’aide du [package NuGet Microsoft.Azure.Notification Hubs].

4. Ouvrez le fichier Program.cs et ajoutez l’instruction `using` suivante :
   
        using Microsoft.Azure.NotificationHubs;

5. Dans la classe `Program` , ajoutez la méthode suivante ou remplacez-la si elle existe déjà :
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Send the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    Ce code envoie une notification de modèle pour chacune des six balises du tableau de chaînes. L’utilisation des balises permet d’envoyer les notifications uniquement aux appareils des catégories inscrites.

5. Dans le code précédent, remplacez les espaces réservés `<hub name>` et `<connection string with full access>` par le nom de votre hub de notification et par la chaîne de connexion pour *DefaultFullSharedAccessSignature* du tableau de bord de votre hub de notifications.

6. Ajoutez les lignes suivantes à la méthode **Main** :
   
         SendTemplateNotificationAsync();
         Console.ReadLine();

7. Générez l’application console.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[prise en main des Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[interface REST des Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[Utilisation de Notification Hubs à partir de Java ou PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[package NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
