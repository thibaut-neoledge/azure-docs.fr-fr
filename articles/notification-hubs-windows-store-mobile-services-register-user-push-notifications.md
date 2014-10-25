<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Inscription de l'utilisateur actif aux notifications Push à l'aide d'un service mobile

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS">iOS</a>
</div>

Cette rubrique vous montre comment demander une inscription aux notifications Push avec Azure Notification Hubs lorsque l'inscription est réalisée par Azure Mobile Services. Cette rubrique s'inscrit dans le prolongement du didacticiel [Notification des utilisateurs avec Notification Hubs][]. Vous devez avoir suivi les étapes de ce didacticiel permettant de créer le service mobile authentifié. Pour plus d'informations sur les scénarios de notification des utilisateurs, consultez la rubrique [Notification des utilisateurs avec Notification Hubs][].

1.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé en suivant le didacticiel préalable [Prise en main de l'authentification][].

2.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

    ![][]

    L'Assistant **Associer votre application au Windows Store** s'affiche.

3.  Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

4.  Sélectionnez l'application inscrite à l'étape [Notification des utilisateurs avec Notification Hubs][], cliquez sur **Suivant**, puis sur **Associer**.

    ![][1]

    Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.

    <div class="dev-callout"><b>Remarque</b>
<p>Cela r&eacute;utilise l'inscription au Windows Store provenant de l'application du didacticiel Notification Hubs avec cette application Mobile Services. Cela peut emp&ecirc;cher l'application du didacticiel Notification Hubs de recevoir des notifications.</p>
</div>

5.  Dans l'Explorateur de solutions, double-cliquez sur le fichier de projet Package.appxmanifest pour l'ouvrir dans l'éditeur de Visual Studio.

6.  Faites défiler jusqu'à **Tous les composants de l'image** et cliquez sur **Logo du badge**. Dans **Notifications**, définissez **Compatible toast** sur **Oui** :

    ![][2]

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

            // Get a channel for push notifications.
            var channel =
                await Windows.Networking.PushNotifications
                .PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Create the body of the request.
            var body = new NotificationRequest 
            {
                channelUri = channel.Uri, 
                platform = "win8" 
            }; 

            try
            {
                // Call the custom API POST method with the supplied body.
                var result = await App.MobileService
                    .InvokeApiAsync<NotificationRequest, 
                    RegistrationResult>("register_notifications", body,
                    System.Net.Http.HttpMethod.Post, null);

                // Set the response, which is the ID of the registration.
                message = string.Format("Registration ID: {0}", result.RegistrationId);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Cette méthode crée un canal pour les notifications Push et l'envoie, avec le type d'appareil, vers la méthode d'API personnalisée qui crée une inscription dans Notification Hubs. Cette API personnalisée a été définie dans le cadre du didacticiel [Notification des utilisateurs avec Notification Hubs][].

9.  Ajoutez la ligne de code suivante à la méthode **OnNavigatedTo**, juste après que la méthode **Authenticate** ait été appelée :

        await RegisterNotification();

    <div class="dev-callout"><b>Remarque</b>
<p>Cela permet de s'assurer que l'inscription est demand&eacute;e &agrave; chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription r&eacute;guli&egrave;rement pour vous assurer de son exactitude.</p>
</div>

Maintenant que l'application cliente est à jour, retournez au didacticiel [Notification des utilisateurs avec Notification Hubs][] et mettez le service mobile à jour pour qu'il envoie des notifications à l'aide de Notification Hubs.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Store C#]: /fr-fr/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "Windows Store C#"
  [iOS]: /fr-fr/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS"
  [Notification des utilisateurs avec Notification Hubs]: /fr-fr/manage/services/notification-hubs/notify-users
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/
  []: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
  [1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
  [2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png
