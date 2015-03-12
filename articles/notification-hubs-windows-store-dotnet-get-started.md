<properties 
	pageTitle="Prise en main d'Azure Notification Hubs" 
	description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications Push." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>

# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal" class="current">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Windows Store ou Windows Phone 8.1 (non Silverlight). Si vous ciblez Windows Phone 8.1 Silverlight, consultez la version [Windows Phone](/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/). 
Le didacticiel vous apprend à créer une application Windows Store vide qui reçoit des notifications Push au moyen du Service de notifications Windows Push (WNS). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Inscription de votre application pour les notifications Push]
2. [Configuration de Notification Hub]
3. [Connexion de votre application au concentrateur de notification]
4. [Envoi de notifications à partir de votre serveur principal]

Ce didacticiel présente un scénario de diffusion simple au moyen de Notification Hubs. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques. Ce didacticiel requiert les éléments suivants :

+ Microsoft Visual Studio Express 2013 pour Windows avec Update 2<br/>Cette version de Visual Studio est requise pour créer un projet d'application universelle. Si vous souhaitez simplement créer une application Windows Store, vous avez besoin de Visual Studio 2012 Express pour Windows 8.

+ Un compte Windows Store actif

+ un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Windows Store. 

##<a name="register"></a>Inscription de votre application pour le Windows Store

Pour envoyer des notifications Push vers des applications Windows Store à partir de Mobile Services, vous devez soumettre votre application au Windows Store. Vous devez ensuite configurer votre Notification Hub pour l'intégrer à WNS.

1. Si vous n'avez pas déjà inscrit votre application, accédez à la page <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Soumettre une application</a> du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

   	![][0]

2. Tapez un nom pour l'application dans **Nom de l'application** et cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

   	![][1]

   	La nouvelle inscription au Windows Store pour votre application est créée.

3. Dans Visual Studio, créez un projet d'application Store Visual C# en utilisant le modèle **Application vide**.

   	![][2]

4. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet d'application Windows Store, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**. 

   	![][3]

   	L'Assistant **Associer votre application au Windows Store** s'affiche.

5. Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6. Cliquez sur l'application inscrite à l'étape 2, puis sur **Suivant** et sur **Associer**.

   	![][4]

   	Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application. 

7. (Facultatif) Répétez les étapes 4 à 6 pour le projet d'application Windows Phone Store.  

7. De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services**. 

   	![][5] 

8. Sur la page **Services**, cliquez sur **Live Services site** sous **Microsoft Azure Mobile Services**.

   	![][17]

9. Dans l'onglet **Paramètres de l'application**, notez les valeurs des options **Clé secrète client** et **Identificateur de sécurité (SID)** du package. 

   	![][6]

 	> [AZURE.NOTE] **Remarque relative à la sécurité**
	La clé secrète client et le SID du package sont des informations d'identification de sécurité importantes. Ne partagez pas ces valeurs avec quiconque et ne les distribuez pas avec votre application.

##<a name="configure-hub"></a>Configuration de Notification Hub

1. Connectez-vous au [portail de gestion Azure] et cliquez sur **NOUVEAU** en bas de l'écran.

2. Cliquez sur **Services d'application**, sur **Service Bus**, sur **Concentrateur de notification**, puis sur **Création rapide**.

   	![][7]

3. Tapez un nom pour votre Notification Hub, sélectionnez une région, puis cliquez sur **Create a new Notification Hub**.

   	![][8]

4. Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du concentrateur de notification*-ns**), puis cliquez sur l'onglet **Configurer** en haut.

   	![][9]

5. Sélectionnez l'onglet **Notification Hubs** en haut, puis cliquez sur celui que vous venez de créer.

   	![][10]

6. Sélectionnez l'onglet **Configurer** en haut, entrez les valeurs de la **Clé secrète client** et du **SID du package** que vous avez obtenues de WNS dans la section précédente, puis cliquez sur **Enregistrer**.

   	![][11]

7. Sélectionnez l'onglet **Tableau de bord** dans la partie supérieure, puis cliquez sur **Informations de connexion**. Notez les deux chaînes de connexion.

   	![][12]

Votre Notification Hub est désormais configuré pour WNS, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications.

##<a name="connecting-app"></a>Connexion de votre application au concentrateur de notification

1. Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet**. 

	La boîte de dialogue Gérer les packages NuGet s'affiche.

2. Recherchez `WindowsAzure.Messaging.Managed` et cliquez sur **Installer**, sélectionnez tous les projets dans la solution, et acceptez les conditions d'utilisation. 

	![][20]

	Cela télécharge, installe et ajoute une référence dans tous les projets à la bibliothèque Messagerie Azure pour Windows en utilisant le <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">package WindowsAzure.Messaging.Managed NuGet</a>. 

3. Ouvrez le fichier de projet App.xaml.cs et ajoutez les instructions `using` suivantes :

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

	Dans un projet universel, ce fichier se trouve dans le dossier `<project_name>.Shared`.

4. Toujours dans le fichier App.xaml.cs, ajoutez la définition de méthode **InitNotificationsAsync** suivante à la classe **App** :
	
	    private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
			
            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
			var result = await hub.RegisterNativeAsync(channel.Uri);
            
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }
	
    Ce code récupère le ChannelURI pour l'application dans WNS et l'inscrit avec votre Notification Hub.

    >[AZURE.NOTE]Remplacez l'espace réservé " hub name " par le nom du Notification Hub affiché sur le portail sous l'onglet **Notification Hubs** (par exemple, **monnotificationhub2** dans l'exemple précédent) : Remplacez également l'espace réservé de la chaîne de connexion par la chaîne de connexion **DefaultListenSharedAccessSignature** que vous avez obtenue dans la section précédente.
    
5. En haut du gestionnaire d'événements **OnLaunched** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync** :

        InitNotificationsAsync();

    Cela garantit que le ChannelURI est inscrit dans votre Notification Hub à chaque fois que l'application est lancée.

6. Dans l'Explorateur de solutions, double-cliquez sur **Package.appxmanifest** de l'application Windows Store, dans **Notifications**, définissez **Compatible toast** sur **Oui** :

   	![][18]

   	Dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

7. (Facultatif) Répétez l'étape précédente dans le projet d'application Windows Phone Store.

8. Appuyez sur la touche **F5** pour exécuter l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.
   
   	![][19]

9. (Facultatif) Répétez l'étape précédente pour exécuter l'autre projet.

Votre application est maintenant prête à recevoir des notifications toast.

##<a name="send"></a>Envoi de notifications à partir de votre serveur principal

Vous pouvez envoyer des notifications à l'aide de Notification Hubs à partir de n'importe quel serveur principal utilisant l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Dans ce didacticiel, vous envoyez des notifications avec une application console .NET. Pour obtenir un exemple expliquant comment envoyer des notifications à partir d'un serveur principal Azure Mobile Services intégré à Notification Hubs, voir **Prise en main des notifications Push dans Mobile Services** ([Service principal .NET](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/) | [Service principal JavaScript](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)).  Pour obtenir un exemple expliquant comment envoyer des notifications à l'aide d'API REST, voir **Utilisation de Notification Hubs depuis Java/PHP** ([Java](/fr-fr/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/fr-fr/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Cliquez avec le bouton droit sur la solution, sélectionnez **Ajouter** et **Nouveau projet...** puis, sous **Visual C#**, cliquez sur **Windows**, sur **Application console** puis sur **OK**. 

   	![][13]

	Une nouvelle application console Visual C# est ajoutée à la solution. Vous pouvez également effectuer cette opération dans une solution distincte. 

4. Cliquez avec le bouton droit sur la solution, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du gestionnaire de package**. 

	La Console du Gestionnaire de package apparaît.

6. Dans la fenêtre de la console, affectez votre nouveau projet d'application console comme **Projet par défaut**, puis exécutez la commande suivante :

        Install-Package WindowsAzure.ServiceBus
    
	Cela permet d'ajouter une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus</a>. 

5. Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.ServiceBus.Notifications;

6. Dans la classe **Program**, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Remplacez l'espace réservé " hub name " par le nom du Notification Hub affiché sur le portail sous l'onglet **Notification Hubs**. Remplacez aussi l'espace réservé de chaîne de connexion par la chaîne de connexion nommée **DefaultFullSharedAccessSignature** que vous avez obtenue dans la section " Configuration de votre concentrateur de notification ". 

	>[AZURE.NOTE]Assurez-vous d'utiliser la chaîne de connexion avec un accès **Total**, et non un accès **Écouter**. La chaîne d'accès Écouter n'est pas autorisée à envoyer des notifications.

7. Ajoutez les lignes suivantes à la méthode **Main** :

         SendNotificationAsync();
		 Console.ReadLine();

8. L'application console étant définie en tant que projet de démarrage, appuyez sur la touche **F5** pour exécuter l'application. 

   	![][14]

	Vous recevrez une notification toast sur tous les appareils enregistrés. En cliquant sur la bannière toast ou en appuyant dessus, l'application se charge.

Vous trouverez toutes les charges utiles prises en charge dans les rubriques du [catalogue toast], du [catalogue de vignettes] et de la [vue d'ensemble des badges] sur MSDN.

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Windows. Pour cibler certains utilisateurs, reportez-vous au didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]. Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]. Pour en savoir plus sur l'utilisation de Notification Hubs, consultez la page [Recommandations relatives à Notification Hubs].

<!-- Anchors. -->
[Inscription de votre application pour les notifications Push]: #register
[Configuration de Notification Hub]: #configure-hub
[Connexion de votre application au concentrateur de notification]: #connecting-app
[Envoi de notifications à partir de votre serveur principal]: #send
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
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
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet
[Notifications Push pour les utilisateurs de l'application]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript et HTML]: /fr-fr/develop/mobile/tutorials/get-started-with-push-js

[Portail de gestion Azure]: https://manage.windowsazure.com/
[objet wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx

[Utilisation de Notification Hubs pour envoyer des notifications aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
[Utilisation de Notification Hubs pour diffuser les dernières nouvelles]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet

[catalogue toast]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[catalogue de vignettes]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[vue d'ensemble des badges]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx

<!--HONumber=45--> 
