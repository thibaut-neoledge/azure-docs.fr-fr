Cette section présente deux façons d'envoyer des notifications :

* [À partir d'une application console](#console)
* [À partir de Mobile Services](#mobile-services)

Ces deux serveurs principaux envoient des notifications aux appareils Windows Store et iOS. Vous pouvez envoyer des notifications à partir d'un serveur principal à l'aide de l'[interface REST des Notification Hubs][1].

<h3><a name="console"></a>Envoi de notifications à partir d'une application de console en C#</h3>


Ignorez les étapes 1 à 3 si vous avez créé une application de console lorsque vous avez effectué la [prise en main des Notification Hubs](/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/).

1.  Dans Visual Studio, créez une application de console Visual C# :
    
    ![][13]

2.  Dans le menu principal de Visual Studio, cliquez sur **Outils**, **Library Package Manager** et **Console du Gestionnaire de package**, puis dans la fenêtre de la console, tapez la ligne suivante et appuyez sur **Entrée** :
    
         Install-Package WindowsAzure.ServiceBus
    
    Une référence au Kit de développement logiciel (SDK) Azure Service Bus est ajoutée à l'aide du [package NuGet WindowsAzure.ServiceBus][2].

3.  Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :
    
         using Microsoft.ServiceBus.Notifications;

4.  Dans la classe `Program`, ajoutez la méthode suivante ou remplacez-la si elle existe déjà :
    
         private static async void SendNotificationAsync()
         {
             // Définissez le notification hub.
             NotificationHubClient hub = 
                 NotificationHubClient.CreateClientFromConnectionString(
                     "<connection  string with full access>", "<hub  name>");
        	
             // Créez un tableau de catégories d’actualités.
             var categories = new string[] { "World", "Politics", "Business", 
                 "Technology", "Science", "Sports"};
        	
             foreach (var category in categories)
             {
                 try
                 {
                     // Définissez un toast Windows Store.
                     var wnsToast = "<toast><visual><binding  template=\"ToastText01\">" 
                         + "<text  id=\"1\">Breaking " + category + " News!" 
                         + "</text></binding></visual></toast>";
                     // Envoyez une notification WNS à l’aide du modèle.            
                     await hub.SendWindowsNativeNotificationAsync(wnsToast, category);
        
                     // Définissez un toast Windows Phone.
                     var mpnsToast =
                         "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                         "<wp :Notification xmlns:wp=\"WPNotification\">" +
                             "<wp :Toast>" +
                                 "<wp :Text1>Breaking " + category + " News!</wp:Text1>" +
                             "</wp:Toast> " +
                         "</wp:Notification>";
                     // Envoyez une notification MPNS à l’aide du modèle.            
                     await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);
        
                     // Définissez une alerte iOS.
                     var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                     // Envoyez une notification APNS à l’aide du modèle.
                     await hub.SendAppleNativeNotificationAsync(alert, category);
                 }
                 catch (ArgumentException)
                 {
                     // Une exception est générée lorsque le notification hub n’a pas été 
                     // enregistré pour la plateforme iOS, Windows Store ou Windows Phone. 
                 }
             }
          }
    
    Ce code envoie des notifications à chacune des six balises du tableau de chaînes aux appareils iOS, Windows Store et Windows Phone. L'utilisation des balises permet d'envoyer les notifications uniquement aux appareils des catégories inscrites.


	**Remarque**

	Ce code principal prend en charge les clients iOS, Windows Store et Windows Phone. Les méthodes d'envoi retournent une réponse d'erreur lorsque le Notification Hub n'a pas encore été configuré pour une plateforme cliente particulière.


5.  Dans le code ci-dessus, remplacez les espaces réservés `<hub name>` et `<connection  string with full access>` par le nom du Notification Hub et la chaîne de connexion pour *DefaultFullSharedAccessSignature* obtenue précédemment.

6.  Ajoutez les lignes suivantes à la méthode **Main** :
    
          SendNotificationAsync();
          Console.ReadLine();

Vous pouvez maintenant poursuivre avec l'[exécution de l'application et la génération des notifications](#test-app).

### <a name="mobile-services"></a>Envoi de notifications à partir de Mobile Services

Pour envoyer une notification à l'aide d'un Mobile Service, procédez comme suit :

1.  Suivez le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started/#create-new-service) pour créer votre service mobile.

2.  Connectez-vous au [portail de gestion Azure][3], cliquez sur Mobile Services, puis sur le service mobile.

3.  Cliquez sur l'onglet **Scheduler**, puis sur **Créer**.
    
       ![][15]

4.  Dans **Créer un travail**, tapez un nom, sélectionnez **À la demande**, puis cliquez sur la coche pour accepter.
    
       ![][16]

5.  Une fois le travail créé, cliquez sur le nom du travail, puis sous l'onglet **Script**, insérez le script suivant dans la fonction travail programmé :
    
        var azure = require(’azure’);
         var notificationHubService = azure.createNotificationHubService(
                 ’<hub  name>’, 
                 ’<connection  string with full access>’);
        
        	 // Créez un tableau de catégories d’actualités.
             var categories = [’World’, ’Politics’, ’Business’, ’Technology’, ’Science’, ’Sports’];
             for (var i = 0; i < categories.length; i++) {
                 // Envoyez une notification WNS.
                 notificationHubService.wns.sendToastText01(categories[i], {
                     text1: ’Breaking ’ + categories[i] + ’ News!’
                 }, sendComplete);
                 // Envoyez une notification MPNS.
                 notificationHubService.mpns.sendToast(categories[i], {
                     text1: ’Breaking ’ + categories[i] + ’ News!’
                 }, sendComplete);
                 // Envoyez une notification APNS.
                 notificationHubService.apns.send(categories[i], {
                     alert: ’Breaking ’ + categories[i] + ’ News!’
                 }, sendComplete);
             }
    
    Ce code envoie des notifications à chacune des six balises du tableau de chaînes aux appareils iOS, Windows Store et Windows Phone. L'utilisation des balises permet d'envoyer les notifications uniquement aux appareils des catégories inscrites.

6.  Dans le code ci-dessus, remplacez les espaces réservés `<hub name>` et `<connection string with full access>` par le nom du Notification Hub et la chaîne de connexion pour *DefaultFullSharedAccessSignature* obtenue précédemment.

7.  Ajoutez la fonction d'assistance suivante après la fonction de travail programmé, puis cliquez sur **Save** :
    
         function sendComplete(error) {
           if (error) {
                 // Une exception est générée lorsqu’aucun appareil n’est enregitré pour 
                 // les plateformes iOS, Windows Store ou Windows Phone. Envisagez plutôt d’utiliser des 
                 // notifications par modèle.
                 //console.warn("Notification failed:" + error);
             }
         }


	**Remarque**

	Ce code prend en charge les clients iOS, Windows Store et Windows Phone. Les méthodes d'envoi retournent une réponse d'erreur en cas l'absence d'inscription pour une plateforme particulière. Pour éviter cela, envisagez l'utilisation d'inscriptions de modèle pour envoyer une seule notification à plusieurs plateformes. Pour voir un exemple, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles localisées](/en-us/manage/services/notification-hubs/breaking-news-localized-dotnet/).



Vous pouvez maintenant poursuivre avec l'[exécution de l'application et la génération des notifications](#test-app).

<!-- Anchors -->

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->



[1]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx
[2]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[3]: https://manage.windowsazure.com/