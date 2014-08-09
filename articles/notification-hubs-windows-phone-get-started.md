<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Prise en main de Notification Hubs
==================================

[Windows Store C#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/fr-fr/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/fr-fr/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/fr-fr/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Windows Phone 8. Ce didacticiel vous apprend à créer une application Windows Phone 8 vide qui reçoit des notifications Push au moyen du Service de notifications Push Microsoft (MPNS). Au final, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre Notification Hub.

Ce didacticiel vous familiarise avec les étapes permettant d'activer les notifications Push :

1.  [Création de votre Notification Hub](#configure-hub)
2.  [Connexion de votre application au Notification Hub](#connecting-app)
3.  [Envoi de notifications à partir de votre serveur principal](#send)

Le didacticiel présente un scénario de diffusion simple en utilisant les Notification Hubs. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques. Ce didacticiel requiert les éléments suivants :

-   [Visual Studio 2012 Express pour Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374) ou version ultérieure.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels concernant les Notification Hubs pour les applications Windows Phone 8.

**Remarque**

Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Création de votre Notification Hub Création de votre Notification Hub
---------------------------------------------------------------------

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/) et cliquez sur **+NEW** au bas de l'écran.

2.  Cliquez sur **App Services**, sur **Service Bus**, sur **Notification Hub**, puis sur **Quick Create**.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png)

3.  Tapez un nom pour votre Notification Hub, sélectionnez la région de votre choix, puis cliquez sur **Create a new Notification Hub**.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png)

4.  Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du Notification Hub*-ns**), puis cliquez sur l'onglet **Configure** en haut.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png)

5.  Cliquez sur l'onglet **Notification Hubs** en haut de la page, puis cliquez sur le Notification Hub que vous venez de créer.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png)

6.  Cliquez sur **Connection Information** au bas de la page. Notez les deux chaînes de connexion.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png)

7.  Cliquez sur l'onglet **Configure**, puis activez la case à cocher **Enable unauthenticated push notifications** dans la section **Windows Phone notifications settings**.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png)

Vous disposez maintenant des chaînes de connexion requises pour inscrire votre application Windows Phone 8 et envoyer des notifications.

**Remarque**

Ce didacticiel utilise MPNS en mode non authentifié. Le mode MPNS non authentifié est assorti de restrictions sur les notifications que vous pouvez envoyer à chaque canal. Notification Hubs prend en charge le [mode MPNS authentifié](http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/ff941099(v=vs.105).aspx).

Connexion de votre applicationConnexion de votre application au Notification Hub
--------------------------------------------------------------------------------

1.  Dans Visual Studio, créez une application Windows Phone 8.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png)

2.  Ajoutez une référence à la bibliothèque Messagerie Azure pour Windows Store en utilisant le [package WindowsAzure.Messaging.Managed NuGet](http://nuget.org/packages/WindowsAzure.Messaging.Managed/). Dans le menu Visual Studio, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du gestionnaire de package**. Dans la fenêtre de la console, tapez :

		Install-Package WindowsAzure.Messaging.Managed

    et appuyez sur Entrée.

3.  Ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante :

		using Microsoft.Phone.Notification;
		using Microsoft.WindowsAzure.Messaging;

4.  Au niveau du code suivant en haut de la méthode **Application_Launching** dans App.xaml.cs :

		var channel = HttpNotificationChannel.Find("MyPushChannel");
		if (channel == null)
		{
			channel = new HttpNotificationChannel("MyPushChannel");
			channel.Open();
			channel.BindToShellToast();
		}

		channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
		{
			var hub = new NotificationHub("<hub name>", "<connection string>");
			await hub.RegisterNativeAsync(args.ChannelUri.ToString());
		});

    Insérez le nom de votre hub et la chaîne de connexion appelée **DefaultListenSharedAccessSignature** obtenue dans la section précédente. Ce code récupère l'URI de canal pour l'application depuis MPNS, puis l'inscrit avec votre Notification Hub. Il garantit également l'inscription de l'URI de canal dans votre Notification Hub chaque fois que l'application est lancée.

    **Remarque**

    Ce didacticiel permet d'envoyer une notification toast à l'appareil. Lorsque vous envoyez une notification par vignette, vous devez appeler la méthode **BindToShellTile** sur le canal. Pour la prise en charge des notifications toast ainsi que des notifications par vignette, appelez **BindToShellTile** et **BindToShellToast**.

5.  Dans l'Explorateur de solutions, développez **Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l'onglet **Fonctionnalités** et veillez à ce que la fonctionnalité **ID__CAP_PUSH_NOTIFICATION** soit activée.

   	![](./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png)

   	Cela garantit que votre application peut recevoir des notifications Push.

6.  Appuyez sur la touche F5 pour exécuter l'application.

Envoi de notificationsEnvoi de notifications à partir de votre serveur principal
--------------------------------------------------------------------------------

Vous pouvez envoyer des notifications en utilisant les Notification Hubs à partir des serveurs principaux en utilisant l'[interface REST](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx). Ce didacticiel permet d'envoyer des notifications avec une application console .NET et avec un service mobile utilisant un script de nœud.

Pour envoyer des notifications en utilisant une application .NET :

1.  Créez une application console Visual C
2.  # :

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png)

2.  Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du [package NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Dans le menu principal de Visual Studio, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du gestionnaire de package**. Dans la fenêtre de la console, tapez :

		Install-Package WindowsAzure.ServiceBus

    et appuyez sur Entrée.

3.  Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :

		using Microsoft.ServiceBus.Notifications;

4.  Dans votre classe `Program`, ajoutez la méthode suivante :

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
			string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
				"<wp:Notification xmlns:wp=\"WPNotification\">" +
					"<wp:Toast>" +
						"<wp:Text1>Hello from a .NET App!</wp:Text1>" +
					"</wp:Toast> " +
				"</wp:Notification>";
			await hub.SendMpnsNativeNotificationAsync(toast);
		}

    Assurez-vous d'insérer le nom de votre hub et la chaîne de connexion appelée DefaultFullSharedAccessSignature que vous avez récupérée dans la section « Configuration de votre Notification Hub ». Notez qu'il s'agit de la chaîne de connexion donnant un accès complet (et non en écoute seule).

5.  Ajoutez ensuite la ligne suivante dans votre méthode Main :

		SendNotificationAsync();
		Console.ReadLine();

6.  Appuyez sur la touche F5 pour exécuter l'application. Vous devez recevoir une notification toast. Vérifiez que votre émulateur Windows Phone s'exécute et que votre application est fermée.

Vous trouverez toutes les charges utiles possibles sur MSDN dans le [catalogue toast](http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/jj662938(v=vs.105).aspx) et le [catalogue de modèles de vignette](http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/hh202948(v=vs.105).aspx).

Étapes suivantes
----------------

Dans cet exemple simple, vous diffusez des notifications à tous vos appareils Windows Phone 8. Pour cibler certains utilisateurs, reportez-vous au didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-aspnet). Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Découvrez plus en détail comment utiliser Notification Hubs dans [Recommandations relatives à Notification Hubs](http://msdn.microsoft.com/fr-fr/library/jj927170.aspx).

