<properties urlDisplayName="Get Started" pageTitle="Prise en main d'Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />
# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Silverlight Windows Phone 8 ou Windows Phone 8.1. Si vous ciblez Windows Phone 8.1 (non-Silverlight), consultez la version  [Windows Universel](/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/) .
Ce didacticiel vous apprend à créer une application Windows Phone 8 vide qui reçoit des notifications Push au moyen du Service de notifications Push Microsoft (MPNS). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification.

> [AZURE.NOTE] Le kit de développement logiciel (SDK) Windows Phone Notification Hubs ne prend pas en charge l'utilisation des services WNS avec les applications Silverlight Windows Phone 8.1. Pour utiliser les services WNS (plutôt que MPNS) avec des applications Silverlight Windows Phone 8.1, vous devez configurer vos informations d'identification WNS comme indiqué dans Prise en main pour Windows Universel. [Prise en main pour Windows Universel	](/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/). Ensuite, vous pouvez vous inscrire à partir du serveur principal comme indiqué dans le didacticiel  [Notification des utilisateurs](/fr-fr/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/)  ou utiliser les  [API REST Notification Hubs](http://msdn.microsoft.com/fr-fr/library/dn223264.aspx).

Ce didacticiel vous familiarise avec les étapes permettant d'activer les notifications Push :

1. [Création de votre concentrateur de notification]
2. [Connexion de votre application au concentrateur de notification]
3. [Envoi de notifications à partir de votre serveur principal]

Le didacticiel présente un scénario de diffusion simple en utilisant Notification Hubs. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques. Ce didacticiel requiert les éléments suivants :

+ [Visual Studio 2012 Express pour Windows Phone] ou version ultérieure.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels concernant les concentrateurs de notification pour les applications Windows Phone 8. 

<div class="dev-callout"><strong>Remarque</strong> <p>RemarquePour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.</p></div>

##<a name="configure-hub"></a>Création de votre concentrateur de notification

1. Connectez-vous au [Portail de gestion Azure]et cliquez sur **+NOUVEAU** en bas de l'écran.

2. Cliquez sur **App Services**, **Service Bus**, **Notification Hub**, puis **Création rapide**.

   	![][7]

3. Tapez un nom pour votre concentrateur de notification, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur de notification**.

   	![][8]

4. Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du concentrateur de notification*-ns**), puis cliquez sur l'onglet **Configurer** en haut.

   	![][9]

5. Cliquez sur l'onglet **Notification Hubs** en haut de la page, puis cliquez sur celui que vous venez de créer.

   	![][10]

6. Cliquez sur **Informations de connexion** au bas de la page. Notez les deux chaînes de connexion.

   	![][12]

7. Cliquez sur l'onglet **Configurer**, puis cochez la case **Activez des notifications push non authentifiées** dans la section **Paramètres de notification de Windows Phone**.

   	![][15]

Vous disposez maintenant des chaînes de connexion requises pour inscrire votre application Windows Phone 8 et envoyer des notifications.

<div class="dev-callout"><b>Remarque</b>
		<p>Ce didacticiel utilise MPNS en mode non authentifié. Le mode MPNS non authentifié est assorti de restrictions sur les notifications que vous pouvez envoyer à chaque canal. Recommandations relatives à Notification Hubs <a href="http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/ff941099(v=vs.105).aspx">Mode MPNS authentifié</a>. <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

##<a name="connecting-app"></a>Connexion de votre application au concentrateur de notification

1. Dans Visual Studio, créez une application Windows Phone 8.

   	![][13]

	Dans Visual Studio 2013 Update 2 ou version ultérieure, vous créez plutôt une application Silverlight Windows Phone.
	
	![][11]	

2. +Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet**. 

	La boîte de dialogue Gérer les packages NuGet s'affiche.

3. Recherchez  `WindowsAzure.Messaging.Managed`  et cliquez sur **Installer** et acceptez les conditions d'utilisation. 

	![][20]

	Une référence à la bibliothèque Messagerie Azure pour Windows est alors téléchargée, installée et ajoutée à l'aide du package NuGet WindowsAzure.Messaging.Managed. <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">package NuGet WindowsAzure.ServiceBus</a>. 

4. Ouvrez le fichier Program.cs et ajoutez l'instruction " using " suivante : `using`  suivante :

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Au niveau du code suivant en haut de la méthode **Application_Launching** dans App.xaml.cs :
	
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

    Insérez le nom de votre concentrateur et la chaîne de connexion appelée **DefaultListenSharedAccessSignature** obtenue dans la section précédente.
    Ce code récupère l'URI de canal pour l'application depuis les services MPNS, puis l'inscrit avec votre concentrateur de notification. Il garantit également l'inscription de l'URI de canal dans votre Notification Hub chaque fois que l'application est lancée.

	>[WACOM.NOTE]Ce didacticiel permet d'envoyer une notification toast à l'appareil. Lorsque vous envoyez une notification par vignette, vous devez appeler la méthode **BindToShellTile** sur le canal. Pour la prise en charge des notifications toast ainsi que des notifications par vignette, appelez **BindToShellTile** et **BindToShellToast**. 
    
6. Dans l'Explorateur de solutions, développez **Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l'onglet **Fonctionnalités** et veillez à ce que la fonctionnalité **ID___CAP___PUSH_NOTIFICATION** soit activée.

   	![][14]

   	Cela garantit que votre application peut recevoir des notifications Push.
	
7. Appuyez sur la touche F5 pour exécuter l'application.

	Un message d'inscription s'affiche.

##<a name="send"></a>Envoi de notifications à partir de votre serveur principal

Vous pouvez envoyer des notifications à l'aide de Notification Hubs à partir de n'importe quel serveur principal utilisant l'interface REST. <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx">interface REST</a>. Dans ce didacticiel, vous envoyez des notifications avec une application console .NET. Pour obtenir un exemple expliquant comment envoyer des notifications à partir d'un serveur principal Azure Mobile Services intégré à Notification Hubs, voir **Prise en main des notifications Push dans Mobile Services** ([Service principal .NET](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/) | [Service principal JavaScript](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)).  Pour obtenir un exemple expliquant comment envoyer des notifications à l'aide d'API REST, voir **Utilisation de Notification Hubs depuis Java/PHP** (Java | PHP).[Java](/fr-fr/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/fr-fr/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Cliquez avec le bouton droit sur la solution, sélectionnez **Ajouter** et **Nouveau projet...** puis, sous **Visual C#**, cliquez sur **Windows**, sur **Application console** puis sur **OK**. 

   	![][6]

	Une nouvelle application console Visual C# est ajoutée à la solution. Vous pouvez également effectuer cette opération dans une solution distincte. 

4. Cliquez avec le bouton droit sur la solution, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du gestionnaire de package**. 

	La Console du Gestionnaire de package apparaît.

6. Dans la fenêtre de la console, affectez votre nouveau projet d'application console comme **Projet par défaut**, puis exécutez la commande suivante :

        Install-Package WindowsAzure.ServiceBus
    
	Cela permet d'ajouter une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du package NuGet WindowsAzure.ServiceBus. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus</a>. 

5. Ouvrez le fichier Program.cs et ajoutez l'instruction " using " suivante :

        using Microsoft.ServiceBus.Notifications;

6. Dans la classe **Program**, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Remplacez l'espace réservé " hub name " par le nom du concentrateur de notifications affiché sur le portail sous l'onglet **Notification Hubs**. Remplacez aussi l'espace réservé de chaîne de connexion par la chaîne de connexion nommée **DefaultFullSharedAccessSignature** que vous avez obtenue dans la section " Configuration de votre concentrateur de notification ". 

	>[WACOM.NOTE]Assurez-vous d'utiliser la chaîne de connexion avec un accès **Total**, et non un accès **Écouter**. La chaîne d'accès Écouter n'est pas autorisée à envoyer des notifications.

4. Ajoutez ensuite la ligne suivante dans votre méthode Main :

         SendNotificationAsync();
		 Console.ReadLine();

5. Votre émulateur Windows Phone étant en cours d'exécution et votre application fermée, configurez le projet d'application console comme projet de démarrage par défaut, puis appuyez sur la touche F5 pour exécuter l'application. 

	Vous recevrez une notification toast. Appuyez sur la bannière du toast pour charger l'application.

Vous trouverez toutes les charges utiles possibles dans le [catalogue toast]  et le  [catalogue de vignettes]  sur MSDN.

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous diffusez des notifications à tous vos appareils Windows Phone 8. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utiliser Notification Hubs pour envoyer des notifications push aux utilisateurs]. Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page  [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]. Découvrez plus en détail comment utiliser Notification Hubs dans [Recommandations relatives à Notification Hubs].

<!-- Anchors. -->
[Création de votre concentrateur de notification]: #configure-hub
[Connexion de votre application au concentrateur de notification]: #connecting-app
[Envoi de notifications à partir de votre serveur principal]: #send
[Étapes suivantes]:#next-steps

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
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/fr-fr/library/jj927170.aspx
[Procédure Notification Hubs pour Windows Phone 8]: tbd!!!
[Mode MPNS authentifié]: http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Utiliser Notification Hubs pour envoyer des notifications push aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet
[catalogue toast]: http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catalogue de vignettes]: http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/hh202948(v=vs.105).aspx
