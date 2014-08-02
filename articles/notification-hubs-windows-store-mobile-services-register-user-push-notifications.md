<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="" solutions="" manager="" editor="" />

Inscription de l'utilisateur actif aux notifications Push à l'aide d'un service mobile
======================================================================================

[Windows Store C\#](/en-us/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "Windows Store C#")[iOS](/en-us/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS")

Cette rubrique vous montre comment demander une inscription aux notifications Push avec Azure Notification Hubs lorsque l'inscription est réalisée par Azure Mobile Services. Cette rubrique s'inscrit dans le prolongement du didacticiel [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users). Vous devez avoir suivi les étapes de ce didacticiel permettant de créer le service mobile authentifié. Pour plus d'informations sur les scénarios de notification des utilisateurs, consultez la rubrique [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users).

1.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé en suivant le didacticiel préalable [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/).

2.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

	![](./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png)

	L'Assistant **Associer votre application au Windows Store** s'affiche.

3.  Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

4.  Sélectionnez l'application inscrite à l'étape [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users), cliquez sur **Suivant**, puis sur **Associer**.

    ![][2]

    Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.  

    **Remarque**

    Cela réutilise l'inscription au Windows Store provenant de l'application du didacticiel Notification Hubs avec cette application Mobile Services. Cela peut empêcher l'application du didacticiel Notification Hubs de recevoir des notifications.

5.  Dans l'Explorateur de solutions, double-cliquez sur le fichier de projet Package.appxmanifest pour l'ouvrir dans l'éditeur de Visual Studio.

6.  Faites défiler jusqu'à **Tous les composants de l'image** et cliquez sur **Logo du badge**. Dans **Notifications**, définissez **Compatible toast** sur **Oui** :

    ![][3]

    Cela permet à l'application du didacticiel Mobile Services de recevoir des notifications toast.

7.  Dans Visual Studio, ouvrez le fichier MainPage.xaml.cs et ajoutez le code suivant qui définit les classes **NotificationRequest** et **RegistrationResult** :

         public class NotificationRequest
         {
             public string channelUri { get; set; }
             public string platform { get; set; }
         }

         public class RegistrationResult
         {
             public string RegistrationId { get; set; }
             public string ExpirationTime { get; set; }
         }

    Ces classes stockent le corps de la requête et l'ID d'inscription renvoyés lorsque l'API personnalisée est appelée, respectivement.

8.  Dans la classe **MainPage**, ajoutez la méthode suivante :

         private async System.Threading.Tasks.Task RegisterNotification()
         {
             string message;

             // Obtenez un canal pour les notifications Push.
             var channel =
                 await Windows.Networking.PushNotifications
                 .PushNotificationChannelManager
                 .CreatePushNotificationChannelForApplicationAsync();

             // Créez le corps de la requête.
             var body = new NotificationRequest 
             {
                 channelUri = channel.Uri, 
                 platform = "win8" 
             }; 

             try
             {
                 // Appelez la méthode API POST personnalisée avec le corps fourni.
                 var result = await App.MobileService
                     .InvokeApiAsync<NotificationRequest, 
                     RegistrationResult>("register_notifications", body,
                     System.Net.Http.HttpMethod.Post, null);

                 // Définissez la réponse, qui est l'ID de l'inscription.
                 message = string.Format("Registration ID: {0}", result.RegistrationId);
             }
             catch (MobileServiceInvalidOperationException ex)
             {
                 message = ex.Message;
             }

             // Affichez une boîte de message.
             var dialog = new MessageDialog(message);
             dialog.Commands.Add(new UICommand("OK"));
             await dialog.ShowAsync();
         }

    Cette méthode crée un canal pour les notifications Push et l'envoie, avec le type d'appareil, vers la méthode d'API personnalisée qui crée une inscription dans Notification Hubs. Cette API personnalisée a été définie dans le cadre du didacticiel [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users).

9.  Ajoutez la ligne de code suivante à la méthode **OnNavigatedTo**, juste après que la méthode **Authenticate** ait été appelée :

         await RegisterNotification();

    **Remarque**

    Cela permet de s'assurer que l'inscription est demandée à chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude.

Maintenant que l'application cliente est à jour, retournez au didacticiel [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users) et mettez le service mobile à jour pour qu'il envoie des notifications à l'aide de Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
[2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
[3]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png

<!-- URLs. -->
[Notify users with Notification Hubs]: /en-us/manage/services/notification-hubs/notify-users
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
