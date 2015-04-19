
Cette section montre comment envoyer des notifications à partir de .NET console application et toutes les autres.
Si vous utilisez des Services mobiles, reportez-vous à la [prise en main Push](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md). Si vous souhaitez utiliser Java ou PHP, consultez la rubrique [Utilisation de Notification Hubs depuis Java/PHP](notification-hubs-java-backend-how-to.md). Vous pouvez envoyer des notifications à partir d'un serveur principal à l'aide de l'[interface REST des Notification Hubs].

Le code suivant envoie des notifications aux appareils Windows Store, Windows Phone, iOS et Android. 

Ignorez les étapes 1 à 3 si vous avez créé une application de console lorsque vous avez effectué la [prise en main des Notification Hubs][get-started].

1. Dans Visual Studio, créez une application de console Visual C# : 

   	![][13]

2. Dans le menu principal de Visual Studio, cliquez sur **Outils**, **Gestionnaire de package de bibliothèque** et **Console du gestionnaire de package**, puis dans la fenêtre de la console, tapez la ligne suivante et appuyez sur **Entrée** :

        Install-Package WindowsAzure.ServiceBus
 	
	Une référence au Kit de développement logiciel (SDK) Azure Service Bus est ajoutée à l'aide du<a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus</a>. 

3. Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.ServiceBus.Notifications;

4. Dans la classe `Program`, ajoutez la méthode suivante ou remplacez-la si elle existe déjà :

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

	Ce code envoie des notifications à chacune des six balises du tableau de chaînes aux appareils iOS, Windows Store et Windows Phone. L'utilisation des balises permet d'envoyer les notifications uniquement aux appareils des catégories inscrites.
	
	> [AZURE.NOTE] Ce code principal prend en charge les clients Windows Store, Windows Phone, iOS et Android. Les méthodes d'envoi retournent une réponse d'erreur lorsque le Notification Hub n'a pas encore été configuré pour une plateforme cliente particulière. 

6. Dans le code ci-dessus, remplacez les espaces réservés <hub name> et <connection string with full access> par le nom du concentrateur de notification et la chaîne de connexion pour  *DefaultFullSharedAccessSignature* obtenue précédemment.

7. Ajoutez les lignes suivantes à la méthode **Main** :

         SendNotificationAsync();
		 Console.ReadLine();

<!-- Anchors -->
[À partir d'une application console]: #console
[À partir de Mobile Services]: #mobile-services
[Exécution de l'application et génération de notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[prise-en-main]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Utilisation de Notification Hubs pour envoyer des notifications aux utilisateurs]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Portail de gestion Azure]: https://manage.windowsazure.com/
[objet wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Vue d'ensemble de Notification Hubs pour Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[interface REST des Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!--HONumber=45--> 
