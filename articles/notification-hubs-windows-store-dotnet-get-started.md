<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Prise en main de Notification Hubs
==================================

[Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Windows Store. Le didacticiel vous apprend à créer une application Windows Store vide qui reçoit des notifications Push au moyen du Service de notifications Windows Push (WNS). Au final, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre Notification Hub.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push](#register)
2.  [Configuration de votre Notification Hub](#configure-hub)
3.  [Connexion de votre application au Notification Hub](#connecting-app)
4.  [Envoi de notifications à partir de votre serveur principal](#send)

Ce didacticiel présente un scénario de diffusion simple au moyen de Notification Hubs. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques. Ce didacticiel requiert les éléments suivants :

-   Microsoft Visual Studio 2012 Express pour Windows 8
-   Un compte Windows Store actif

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Windows Store.

**Remarque**

Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Inscription de votre applicationInscription de votre application pour le Windows Store
--------------------------------------------------------------------------------------

Pour envoyer des notifications Push vers des applications Windows Store à partir de Mobile Services, vous devez soumettre votre application au Windows Store. Vous devez ensuite configurer votre Notification Hub pour l'intégrer à WNS.

1.  Si vous n'avez pas déjà inscrit votre application, accédez à la page [Soumettre une application](http://go.microsoft.com/fwlink/p/?LinkID=266582) du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

	![][0]

2.  Tapez un nom pour l'application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

	![][1]

	La nouvelle inscription au Windows Store pour votre application est créée.

3.  Dans Visual Studio 2012 Express pour Windows 8, créez un projet Visual C\# Windows Store en utilisant le modèle **Blank App**.

	![][2]

4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

	![][3]

	L'Assistant **Associer votre application au Windows Store** s'affiche.

5.  Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6.  Cliquez sur l'application inscrite à l'étape 2, puis sur **Suivant** et sur **Associer**.

	![][4]

	Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.    

7.  De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services**.

	![][5] 

8.  Sur la page **Services**, cliquez sur **Live Services site** sous **Azure Mobile Services**.

	![][17]

9.  Cliquez sur **Authentification de votre service** et notez les valeurs des options **Clé secrète client** et **Identificateur de sécurité (SID) du package**.

	![][6]

	<div class="dev-callout"\><b>Remarque relative à la sécurité</b>

    <p>La clé secrète cliente et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces informations secrètes avec quiconque et ne les distribuez pas avec votre application.</p>
    </div>

Configuration de votre Notification HubConfiguration de votre Notification Hub
------------------------------------------------------------------------------

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/) et cliquez sur **NOUVEAU** en bas de l'écran.

2.  Cliquez sur **App Services**, puis sur **Service Bus**, **Notification Hub**, **Création rapide**.

	![][7]

3.  Tapez un nom pour votre Notification Hub, sélectionnez une région, puis cliquez sur **Create a new Notification Hub**.

	![][8]

4.  Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du Notification Hub*-ns**), puis cliquez sur l'onglet **Configure** en haut.

	![][9]

5.  Sélectionnez l'onglet **Notification Hubs** en haut, puis cliquez sur le Notification Hub que vous venez de créer.

	![][10]

6.  Sélectionnez l'onglet **Configure** en haut, entrez les valeurs de la **Clé secrète client** et du **SID du package** que vous avez obtenues de WNS dans la section précédente, puis cliquez sur **Enregistrer**.

	![][11]

7.  Sélectionnez l'onglet **Dashboard** en haut, puis cliquez sur **Connection Information**. Notez les deux chaînes de connexion.

	![][12]

Votre Notification Hub est désormais configuré pour WNS, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications.

Connexion de votre applicationConnexion de votre application au Notification Hub
--------------------------------------------------------------------------------

1.  Ajoutez une référence à la bibliothèque Messagerie Azure pour Windows Store en utilisant le [package WindowsAzure.Messaging.Managed NuGet](http://nuget.org/packages/WindowsAzure.Messaging.Managed/). Dans le menu principal de Visual Studio, cliquez sur **Outils**, **Library Package Manager**, puis sur **Console du gestionnaire de package**. Dans la fenêtre de la console, tapez :

         Install-Package WindowsAzure.Messaging.Managed

    puis appuyez sur **Entrée**.

2.  Ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante :

         using Windows.Networking.PushNotifications;
         using Microsoft.WindowsAzure.Messaging;
         using Windows.UI.Popups;

3.  Ajoutez le code suivant à App.xaml.cs, dans la classe `App`. Remplacez l'espace réservé « hub name » par le nom du Notification Hub affiché sur le portail sous l'onglet **Notification Hubs** (par exemple, **monnotificationhub2** dans l'exemple précédent) :

         private async void InitNotificationsAsync()
         {
             var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
                
             var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
             var result = await hub.RegisterNativeAsync(channel.Uri);
                
             // Affiche l'ID d'inscription pour indiquer la réussite
             if (result.RegistrationId != null)
             {
                 var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
             }

         }

    Insérez le nom de votre hub et la chaîne de connexion appelée **DefaultListenSharedAccessSignature** obtenue dans la section précédente. Ce code récupère le ChannelURI pour l'application dans WNS et l'inscrit avec votre Notification Hub.

4.  En haut du gestionnaire d'événements **OnLaunched** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync** :

         InitNotificationsAsync();

    Cela garantit que le ChannelURI est inscrit dans votre Notification Hub à chaque fois que l'application est lancée.

5.  Appuyez sur **F5** pour lancer l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.

	![][19]

Envoi de notificationsEnvoi de notifications à partir de votre serveur principal
--------------------------------------------------------------------------------

Vous pouvez envoyer des notifications en utilisant les Notification Hubs à partir des serveurs principaux en utilisant l'[interface REST](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx). Dans ce didacticiel, vous envoyez des notifications avec une application console .NET et un Mobile Service avec un script de nœud.

Pour envoyer des notifications en utilisant une application .NET :

1.  Avec la solution précédente ouverte dans Visual Studio, dans l'Explorateur de solutions, double-cliquez sur **Package.appxmanifest** pour l'ouvrir dans l'éditeur Visual Studio.

2.  Faites défiler jusqu'à **Tous les composants de l'image** et cliquez sur **Logo du badge**. Dans **Notifications**, définissez **Compatible toast** sur **Oui** :

	![][18]

	Dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

3.  Créez une application console Visual C\# :

	![][13]

4.  Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du [package NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Dans le menu principal de Visual Studio, cliquez sur **Outils**, **Library Package Manager**, puis sur **Console du gestionnaire de package**. Dans la fenêtre de la console, tapez :

         Install-Package WindowsAzure.ServiceBus

    puis appuyez sur **Entrée**.

5.  Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :

         using Microsoft.ServiceBus.Notifications;

6.  Dans la classe `Program`, ajoutez la méthode suivante. Remplacez l'espace réservé « hub name » par le nom du Notification Hub affiché sur le portail sous l'onglet **Notification Hubs** :

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
             await hub.SendWindowsNativeNotificationAsync(toast);
         }

        Insérez le nom de votre hub et la chaîne de connexion appelée **DefaultFullSharedAccessSignature** obtenue dans la section « Configuration de votre Notification Hub ». Notez qu'il s'agit de la chaîne de connexion avec l'accès **total**, pas avec l'accès **Écouter**.

7.  Ajoutez les lignes suivantes à la méthode `Main` :

          SendNotificationAsync();
          Console.ReadLine();

8.  Appuyez sur **F5** pour lancer l'application. Vous devez recevoir une notification toast.

	![][14]

Vous trouverez toutes les charges utiles possibles sur MSDN dans le [catalogue toast](http://msdn.microsoft.com/en-us/library/windows/apps/hh761494.aspx), le [catalogue de vignettes](http://msdn.microsoft.com/en-us/library/windows/apps/hh761491.aspx) et dans la [vue d'ensemble des badges](http://msdn.microsoft.com/en-us/library/windows/apps/hh779719.aspx).

Pour envoyer une notification en utilisant un service Mobile Service, suivez les instructions de la rubrique [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started/#create-new-service), puis procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/) et cliquez sur votre service mobile.

2.  Sélectionnez l'onglet **Scheduler** en haut.

	![][15]

3.  Créez un nouveau travail planifié, insérez un nom, puis cliquez sur **À la demande**.

	![][16]

4.  Lorsque le travail est créé, cliquez sur son nom. Cliquez ensuite sur l'onglet **Script** dans la barre supérieure.

5.  Insérez le script suivant dans votre fonction de planificateur. Remplacez les espaces réservés par le nom du Notification Hub et la chaîne de connexion pour *DefaultFullSharedAccessSignature* obtenue précédemment. Lorsque vous avez terminé, cliquez sur **Enregistrer** sur la barre inférieure.

         var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
         notificationHubService.wns.sendToastText01(
             null,
             {
                 text1: 'Hello from Mobile Services!!!'
             },
             function (error) {
                 if (!error) {
                     console.warn("Notification successful");
                 }
         });

6.  Cliquez sur **Exécuter une fois** sur la barre inférieure. Vous devez recevoir une notification toast.

Étapes suivantes
----------------

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Windows. Pour cibler certains utilisateurs, reportez-vous au didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-aspnet). Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Pour plus d'informations sur l'utilisation des Notification Hubs, consultez les pages [Vue d'ensemble des concentrateurs de notification](http://msdn.microsoft.com/en-us/library/jj927170.aspx) et [Procédures Notification Hubs pour Windows Store](http://msdn.microsoft.com/en-us/library/jj927172.aspx).

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png