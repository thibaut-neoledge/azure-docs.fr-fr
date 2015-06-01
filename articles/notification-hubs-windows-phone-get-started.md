<properties 
	pageTitle="Prise en main d’Azure Notification Hubs" 
	description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications Push." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>
# Prise en main de Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Vue d'ensemble

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Silverlight Windows Phone 8 ou Windows Phone 8.1. Si vous ciblez Windows Phone 8.1 (non-Silverlight), consultez la version [Windows Universel](notification-hubs-windows-store-dotnet-get-started.md). Ce didacticiel vous apprend à créer une application Windows Phone 8 vide qui reçoit des notifications Push au moyen du Service de notifications Push Microsoft (MPNS). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification.

> [AZURE.NOTE]Le kit de développement logiciel (SDK) Windows Phone Notification Hubs ne prend pas en charge l’utilisation des services WNS avec les applications Silverlight Windows Phone 8.1. Pour utiliser WNS (au lieu de MPNS) avec les applications Windows Phone 8.1 Silverlight, suivez l’exemple [Notification Hubs - Didacticiel WP Silverlight], qui s’appuie sur des API REST.

Le didacticiel présente un scénario de diffusion simple en utilisant les Notification Hubs.

##Configuration requise

Ce didacticiel requiert les éléments suivants :

+ [Visual Studio 2012 Express pour Windows Phone] ou version ultérieure.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels concernant les Notification Hubs pour les applications Windows Phone 8.

> [AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

##Création de votre concentrateur de notification

1. Connectez-vous au [portail de gestion Azure] et cliquez sur **+NEW** au bas de l'écran.

2. Cliquez sur **App Services**, sur **Service Bus**, sur **Notification Hub**, puis sur **Quick Create**.

   ![][7]

3. Tapez un nom pour votre Notification Hub, sélectionnez la région de votre choix, puis cliquez sur **Create a new Notification Hub**.

   ![][8]

4. Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du Notification Hub*-ns**), puis cliquez sur l'onglet **Configure** en haut.

   ![][9]

5. Cliquez sur l'onglet **Concentrateurs de notification** en haut de la page, puis cliquez sur le concentrateur de notification que vous venez de créer.

   ![][10]

6. Cliquez sur **Connection Information** au bas de la page. Notez les deux chaînes de connexion.

   ![][12]

7. Cliquez sur l'onglet **Configurer**, puis cochez la case **Activez des notifications push non authentifiées** dans la section **Paramètres de notification de Windows Phone**.

   ![][15]

Vous disposez maintenant des chaînes de connexion requises pour inscrire votre application Windows Phone 8 et envoyer des notifications.

> [AZURE.NOTE]Ce didacticiel utilise MPNS en mode non authentifié. Le mode MPNS non authentifié est assorti de restrictions sur les notifications que vous pouvez envoyer à chaque canal. Notification Hubs prend en charge le [mode MPNS authentifié](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##Connexion de votre application au concentrateur de notification

1. Dans Visual Studio, créez une application Windows Phone 8.

   ![][13]

	In Visual Studio 2013 Update 2 or later, you instead create a Windows Phone Silverlight application.
	
	![][11]	

2. Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis cliquez sur **Manage NuGet Packages**. 

	La boîte de dialogue Gérer les packages NuGet s'affiche.

3. Recherchez `WindowsAzure.Messaging.Managed`, puis cliquez sur **Installer** et acceptez les conditions d’utilisation.

	![][20]

	Une référence à la bibliothèque Azure Messaging pour Windows est alors téléchargée, installée et ajoutée à l’aide du <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">package NuGet WindowsAzure.Messaging.Managed</a>.

4. Ouvrez le fichier App.xaml.cs et ajoutez les instructions `using` suivantes :

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Au niveau du code suivant en haut de la méthode **Application_Launching** dans App.xaml.cs :
	
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

	>[AZURE.NOTE]Ce didacticiel permet d'envoyer une notification toast à l'appareil. Lorsque vous envoyez une notification par vignette, vous devez appeler la méthode **BindToShellTile** sur le canal. Pour la prise en charge des notifications toast ainsi que des notifications par vignette, appelez **BindToShellTile** et **BindToShellToast**.
    
6. Dans l’Explorateur de solutions, développez **Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l’onglet **Fonctionnalités** et veillez à ce que la fonctionnalité **ID___CAP___PUSH_NOTIFICATION** soit activée.

   ![][14]

   Cela garantit que votre application peut recevoir des notifications Push.
	
7. Appuyez sur la touche F5 pour exécuter l'application.

	Un message d'inscription s'affiche.

##Envoi de notifications à partir de votre serveur principal

Vous pouvez envoyer des notifications à l’aide de Notification Hubs à partir de n’importe quel serveur principal utilisant l’<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Dans ce didacticiel, vous envoyez des notifications avec une application console .NET. Pour découvrir un exemple d'envoi de notifications depuis un serveur principal Azure Mobile Services intégré à Notification Hubs, consultez **Prise en main des notifications Push dans Mobile Services** ([.NET backend](mobile-services-javascript-backend-windows-phone-get-started-push.md) | [JavaScript backend](mobile-services-javascript-backend-windows-phone-get-started-push.md)). Pour voir un exemple d'envoi de notifications au moyen des API REST, consultez la rubrique **Utilisation de Notification Hubs depuis Java/PHP** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Cliquez avec le bouton droit sur la solution, sélectionnez **Ajouter** et **Nouveau projet...**, puis sous **Visual C#** cliquez sur **Windows** et **Application Console**, puis cliquez sur **OK**. 

   ![][6]

	Une nouvelle application console Visual C# est ajoutée à la solution. Vous pouvez également effectuer cette opération dans une solution distincte. 
 

4. Cliquez avec le bouton droit sur **Outils**, puis cliquez sur **Library Package Manager**, et enfin sur **Console du gestionnaire de package**. 

	La console du gestionnaire de package s'affiche.

6. Dans la fenêtre de la console, définissez **Projet par défaut** sur votre nouveau projet d'application console, puis dans la fenêtre de la console exécutez la commande suivante :

        Install-Package WindowsAzure.ServiceBus
    
	Une référence est ajoutée au Kit de développement logiciel (SDK) Azure Service Bus à l’aide du <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus</a>.

5. Ouvrez le fichier Program.cs et ajoutez l’instruction `using` suivante :

        using Microsoft.ServiceBus.Notifications;

6. Dans la classe **Program**, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Remplacez l'espace réservé « hub name » par le nom du Notification Hub affiché sur le portail sous l'onglet **Notification Hubs**. Remplacez également l'espace réservé de la chaîne de connexion par la chaîne de connexion appelée **DefaultFullSharedAccessSignature** que vous avez obtenue dans la section « Configuration de votre Notification Hub ».

	>[AZURE.NOTE]Assurez-vous d’utiliser la chaîne de connexion avec un accès **Total**, et non un accès **Écouter**. La chaîne d'accès en écoute seule ne dispose pas des autorisations pour envoyer des notifications.

4. Ajoutez ensuite la ligne suivante dans votre méthode Main :

         SendNotificationAsync();
		 Console.ReadLine();

5. Votre émulateur Windows Phone étant en cours d'exécution et votre application fermée, configurez le projet d'application console comme projet de démarrage par défaut, puis appuyez sur la touche F5 pour exécuter l'application.

	Vous recevrez une notification toast. Un appui sur la bannière du toast charge l'application.

Vous trouverez toutes les charges utiles possibles dans le [catalogue toast] et le [catalogue de modèles de vignette] sur MSDN.

##Étapes suivantes

Dans cet exemple simple, vous diffusez des notifications à tous vos appareils Windows Phone 8. Pour cibler certains utilisateurs, reportez-vous au didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]. Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]. Découvrez plus en détail comment utiliser Notification Hubs dans [Recommandations relatives à Notification Hubs].



<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express pour Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[portail de gestion Azure]: https://manage.windowsazure.com/
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[mode MPNS authentifié]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: notification-hubs-windows-phone-send-breaking-news.md
[catalogue toast]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catalogue de modèles de vignette]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs - Didacticiel WP Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp


<!--HONumber=52-->
