
Cette section montre comment envoyer les dernières nouvelles sous forme de notifications modèles avec balises à partir de l’application console .NET.

Si vous utilisez Mobile Apps, reportez-vous au didacticiel [Add push notifications for Mobile Apps](../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) (Ajouter des notifications Push pour Mobile Apps).

Si vous souhaitez utiliser Java ou PHP, consultez la rubrique [Utilisation de Notification Hubs depuis Java/PHP](../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md). Vous pouvez envoyer des notifications à partir d’un serveur principal à l’aide de l’[interface REST de Notification Hub](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

Ignorez les étapes 1 à 3 si vous avez créé l’application de console pour l’envoi des notifications lorsque vous avez effectué la [prise en main des Notification Hubs][get-started].

1. Dans Visual Studio, créez une application console Visual C# : 

   	![][13]

2. Dans le menu principal de Visual Studio, cliquez sur **Outils**, **Gestionnaire de package de bibliothèques** et **Console du gestionnaire de package**, puis dans la fenêtre de la console, tapez la ligne suivante et appuyez sur **Entrée** :

        Install-Package Microsoft.Azure.NotificationHubs
 	
	Cette opération ajoute une référence au Kit de développement logiciel (SDK) Azure Notification Hubs à l’aide du <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">package NuGet Microsoft.Azure.Notification Hubs</a>.

3. Ouvrez le fichier Program.cs et ajoutez l’instruction `using` suivante :

        using Microsoft.Azure.NotificationHubs;

4. Dans la classe `Program`, ajoutez la méthode suivante ou remplacez-la si elle existe déjà :

        private static async void SendTemplateNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
											"Technology", "Science", "Sports"};

            // Sending the notification as a template notification. All template registrations that contain 
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

6. Dans le code ci-dessus, remplacez les espaces réservés `<hub name>` et `<connection string with full access>` par le nom de votre hub de notification et par la chaîne de connexion pour *DefaultFullSharedAccessSignature* du tableau de bord de votre hub de notifications.

7. Ajoutez les lignes suivantes à la méthode **Main** :

         SendTemplateNotificationAsync();
		 Console.ReadLine();

8. Générez l’application console.

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to send notifications to users]: ../articles/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!---HONumber=AcomDC_0622_2016-->