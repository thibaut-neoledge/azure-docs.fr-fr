<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" writer="wesmc" manager="" editor="" />

Prise en main des notifications Push dans Mobile Services
=========================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push "Windows Store C#") [Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push "Windows Store JavaScript") [Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push "Windows Phone")
[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/ "JavaScript backend")

Cette rubrique montre comment utiliser le runtime .NET Windows Azure dans Mobile Services pour envoyer des notifications Push à une application Windows Phone 8. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide d'Azure Notification Hubs. Une fois la procédure terminée, votre service mobile .Net runtime envoie une notification Push à l'aide de Notification Hubs chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Mise à jour de l'application pour l'inscription aux notifications](#update-app)
2.  [Mise à jour du serveur pour l'envoi de notifications Push](#update-server)
3.  [Insertion de données pour recevoir des notifications Push](#test)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-get-started) ou [Prise en main des données](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data) pour connecter votre projet au service mobile.

Mise à jour de l'application pour l'inscription aux notifications
-----------------------------------------------------------------

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1.  Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez les instructions `using` suivantes :

         using Microsoft.Phone.Notification;

2.  Ajoutez la méthode `AcquirePushChannel` suivante à la classe `App` :

         private void AcquirePushChannel()
         {
             CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");
             if (CurrentChannel == null)
             {
                 CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                 CurrentChannel.Open();
                 CurrentChannel.BindToShellToast();
             }
             CurrentChannel.ChannelUriUpdated +=
                 new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                 {
                     // Register for notifications using the new channel
                     System.Exception exception = null;
                     try
                     {
                         await MobileService.GetPush()
                             .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                     }
                     catch (System.Exception ex)
                     {
                         CurrentChannel.Close();
                         exception = ex;
                     }
                     if (exception != null)
                     {
                         Deployment.Current.Dispatcher.BeginInvoke(() =>
                         {
                             MessageBox.Show(exception.Message, 
                                             "Registering for Push Notifications",
                                             MessageBoxButton.OK);
                         });
                     }
             });
             CurrentChannel.ShellToastNotificationReceived += 
                 new EventHandler<NotificationEventArgs>((o, args) =>
                 {
                     string message = "";
                     foreach (string key in args.Collection.Keys)
                     {
                         message += key + " : " + args.Collection[key] + ", ";
                     }
                     Deployment.Current.Dispatcher.BeginInvoke(() =>
                     {
                         MessageBox.Show(message);
                     });
             });
         }

    Ce code permet de récupérer l'URI de canal de l'application, s'il existe. S'il n'existe pas, il est créé. L'URI de canal est ensuite ouvert et lié aux notifications toast. Une fois l'URI de canal ouvert, le gestionnaire de la méthode `ChannelUriUpdated` est appelé et le canal est inscrit aux notifications Push. En cas d'échec de l'inscription, le canal est fermé afin de permettre aux exécutions suivantes de l'application de tenter une nouvelle inscription. Le gestionnaire `ShellToastNotificationReceived` est configuré de manière à permettre à l'application de recevoir et de gérer les notifications Push pendant son exécution.

3.  Dans le gestionnaire d'événements `Application_Launching` du fichier App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode `AcquirePushChannel` :

         AcquirePushChannel();

    L'inscription est ainsi demandée chaque fois que l'application est chargée. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude.

4.  Appuyez sur la touche **F5** pour exécuter l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.

5.  Dans Visual Studio, ouvrez le fichier Package.appxmanifest et vérifiez que **Compatible toast** est défini sur **Oui** dans l'onglet **Interface utilisateur de l'application**.

	![][1]

	Cela vous permet de vous assurer que votre application peut déclencher des notifications toast. 

Mise à jour du serveur pour l'envoi de notifications Push
---------------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

1.  Connectez-vous au [portail de gestion Windows Azure](%20https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

2.  Cliquez sur l'onglet **Push**, cochez **Activez des notifications push non authentifiées**, puis cliquez sur **Enregistrer**.

![](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png)

> [WACOM.NOTE] Ce didacticiel utilise MPNS en mode non authentifié. Dans ce mode, MPNS limite le nombre de notifications à envoyer à un canal d'appareil. Pour supprimer cette restriction, vous devez générer et télécharger un certificat en cliquant sur **Télécharger** et en sélectionnant le certificat. Pour plus d'informations sur la génération du certificat, consultez la rubrique [Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone](http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx).

Le service mobile peut ainsi se connecter à MPNS en mode non authentifié pour envoyer les notifications Push.

Test des notifications Push dans votre application
--------------------------------------------------

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Dans l'application, entrez le texte « hello push » dans la zone de texte, puis cliquez sur **Enregistrer**.

	![][2]

	Cela envoie une requête insert au service mobile pour stocker l'élément ajouté. Notez que l'application reçoit une notification toast qui indique **hello push**.

Étapes suivantes
----------------

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

-   [Envoi de notifications aux abonnés](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

-   [Envoi de notifications aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>En savoir plus sur l'envoi de notifications Push depuis un service mobile à certains utilisateurs sur n'importe quel appareil.

-   [Envoi de notifications interplateforme aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)
    <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles .Net runtime.

-   [Prise en main de l'authentification](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles .Net runtime.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services .NET](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
    <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push

[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
