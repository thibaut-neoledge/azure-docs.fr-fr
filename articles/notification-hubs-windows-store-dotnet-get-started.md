<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows universel" class="current">Windows universel</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Windows Store ou Windows Phone 8.1 (non Silverlight). Si vous ciblez Silverlight Windows Phone 8.1, consultez la version [Windows Phone][1].
Le didacticiel vous apprend à créer une application Windows Store vide qui reçoit des notifications Push au moyen du Service de notifications Windows Push (WNS). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push][]
2.  [Configuration de votre concentrateur de notification][]
3.  [Connexion de votre application au concentrateur de notification][]
4.  [Envoi de notifications à partir de votre serveur principal][]

Ce didacticiel présente un scénario de diffusion simple au moyen de Notification Hubs. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques. Ce didacticiel requiert les éléments suivants :

-   Microsoft Visual Studio Express 2013 pour Windows avec Update 2
    Cette version de Visual Studio est requise pour créer un projet d'application universelle. Si vous souhaitez simplement créer une application Windows Store, vous avez besoin de Visual Studio 2012 Express pour Windows 8.

-   Un compte Windows Store actif

-   Un compte Azure actif.
    Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][].

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Windows Store.

## <a name="register"></a>Inscription de votre application pour le Windows Store

Pour envoyer des notifications Push vers des applications Windows Store à partir de Mobile Services, vous devez soumettre votre application au Windows Store. Vous devez ensuite configurer votre Notification Hub pour l'intégrer à WNS.

1.  Si vous n'avez pas déjà inscrit votre application, accédez à la page [Soumettre une application][] du Centre de développement pour les applications Windows Store, connectez-vous à votre compte Microsoft, puis cliquez sur le **Nom de l'application**.

    ![][]

2.  Tapez un nom pour l'application dans **Nom de l'application**, cliquez sur **Réserver le nom d'application**, puis sur **Enregistrer**.

    ![][2]

    La nouvelle inscription au Windows Store pour votre application est créée.

3.  Dans Visual Studio, créez un projet d'application Store Visual C# en utilisant le modèle **Application vide**.

    ![][3]

4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet d'application Windows Store, cliquez sur **Store**, puis sur **Associer l'application au Windows Store...**.

    ![][4]

    L'Assistant **Associer votre application au Windows Store** s'affiche.

5.  Dans l'Assistant, cliquez sur **Se connecter**, puis connectez-vous avec votre compte Microsoft.

6.  Cliquez sur l'application inscrite à l'étape 2, puis sur **Suivant** et sur **Associer**.

    ![][5]

    Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.

7.  (Facultatif) Répétez les étapes 4 à 6 pour le projet d'application Windows Phone Store.

8.  De retour sur la page du centre de développement Windows pour la nouvelle application, cliquez sur **Services**.

    ![][6]

9.  Sur la page **Services**, cliquez sur **Live Services site** sous **Microsoft Azure Mobile Services**.

    ![][7]

10. Dans l'onglet **Paramètres de l'application**, notez les valeurs des options **Clé secrète client** et **Identificateur de sécurité (SID) du package**.

    ![][8]

    <div class="dev-callout"><b>Remarque relative &agrave; la s&eacute;curit&eacute;</b>
<p>La cl&eacute; secr&egrave;te client et le SID du package sont des informations d'identification de s&eacute;curit&eacute; importantes. Ne partagez pas ces valeurs avec quiconque et ne les distribuez pas avec votre application.</p>
</div>

## <a name="configure-hub"></a>Configuration de votre Notification Hub

1.  Connectez-vous au [portail de gestion Azure][] et cliquez sur **NOUVEAU** en bas de l'écran.

2.  Cliquez sur **Services d'application**, puis sur **Service Bus**, **Concentrateur de notification**, **Création rapide**.

    ![][9]

3.  Tapez un nom pour votre Notification Hub, sélectionnez une région, puis cliquez sur **Create a new Notification Hub**.

    ![][10]

4.  Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du Notification Hub*-ns**), puis cliquez sur l'onglet **Configure** en haut.

    ![][11]

5.  Sélectionnez l'onglet **Notification Hubs** en haut, puis cliquez sur le Notification Hub que vous venez de créer.

    ![][12]

6.  Sélectionnez l'onglet **Configure** en haut, entrez les valeurs de la **Clé secrète client** et du **SID du package** que vous avez obtenues de WNS dans la section précédente, puis cliquez sur **Enregistrer**.

    ![][13]

7.  Sélectionnez l'onglet **Dashboard** en haut, puis cliquez sur **Connection Information**. Notez les deux chaînes de connexion.

    ![][14]

Votre Notification Hub est désormais configuré pour WNS, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications.

## <a name="connecting-app"></a>Connexion de votre application au Notification Hub

1.  Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis cliquez sur **Manage NuGet Packages**.

    La boîte de dialogue Manage NuGet Packages apparaît.

2.  Recherchez `WindowsAzure.Messaging.Managed` et cliquez sur **Installer**, sélectionnez tous les projets dans la solution, et acceptez les conditions d'utilisation.

    ![][15]

    Cela télécharge, installe et ajoute une référence dans tous les projets à la bibliothèque Messagerie Azure pour Windows en utilisant le [package WindowsAzure.Messaging.Managed NuGet][].

3.  Ouvrez le fichier de projet App.xaml.cs et ajoutez les instructions `using` suivantes :

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

    Dans un projet universel, ce fichier se trouve dans le dossier `<project_name>.Shared`.

4.  Toujours dans le fichier App.xaml.cs, ajoutez la définition de méthode **InitNotificationsAsync** suivante à la classe **App** :

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

    > [WACOM.NOTE]Remplacez l'espace réservé « hub name » par le nom du Notification Hub affiché sur le portail sous l'onglet **Notification Hubs** (par exemple, **mynotificationhub2** dans l'exemple précédent). Remplacez également l'espace réservé de la chaîne de connexion par la chaîne de connexion **DefaultListenSharedAccessSignature** que vous avez obtenue dans la section précédente.

5.  En haut du gestionnaire d'événements **OnLaunched** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **InitNotificationsAsync** :

        InitNotificationsAsync();

    Cela garantit que le ChannelURI est inscrit dans votre Notification Hub à chaque fois que l'application est lancée.

6.  Dans l'Explorateur de solutions, double-cliquez sur **Package.appxmanifest** de l'application Windows Store, dans **Notifications**, définissez **Compatible toast** sur **Oui** :

    ![][16]

    Dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

7.  (Facultatif) Répétez l'étape précédente dans le projet d'application Windows Phone Store.

8.  Appuyez sur **F5** pour lancer l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.

    ![][17]

9.  (Facultatif) Répétez l'étape précédente pour exécuter l'autre projet.

Votre application est maintenant prête à recevoir des notifications toast.

## <a name="send"></a>Envoi de notifications à partir de votre serveur principal

Vous pouvez envoyer des notifications en utilisant Notification Hubs à partir de tous les serveurs principaux qui utilisent l'[interface REST][]. Dans ce didacticiel, vous envoyez des notifications avec une application console .NET. Pour découvrir un exemple d'envoi de notifications depuis un serveur principal Azure Mobile Services intégré à Notification Hubs, consultez **Prise en main des notifications Push dans Mobile Services** ([.NET backend][] | [JavaScript backend][.NET backend]). Pour voir un exemple d'envoi de notifications au moyen des API REST, consultez la rubrique **Utilisation de Notification Hubs depuis Java/PHP** ([Java][] | [PHP][]).

1.  Cliquez avec le bouton droit sur la solution, sélectionnez **Ajouter** et **Nouveau projet...**, puis sous **Visual C#** cliquez sur **Windows** et **Application Console**, puis cliquez sur **OK**.

    ![][18]

    Une nouvelle application console Visual C# est ajoutée à la solution. Vous pouvez également réaliser cette opération dans une solution distincte.

2.  Cliquez avec le bouton droit sur **Outils**, puis cliquez sur **Library Package Manager**, et enfin sur **Console du gestionnaire de package**.

    La console du gestionnaire de package s'affiche.

3.  Dans la fenêtre de la console, définissez **Projet par défaut** sur votre nouveau projet d'application console, puis dans la fenêtre de la console exécutez la commande suivante :

        Install-Package WindowsAzure.ServiceBus

    Une référence est ajoutée au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du [package NuGet WindowsAzure.ServiceBus][].

4.  Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.ServiceBus.Notifications;

5.  Dans la classe **Program**, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

    Remplacez l'espace réservé « hub name » par le nom du Notification Hub affiché sur le portail sous l'onglet **Notification Hubs**. Remplacez également l'espace réservé de la chaîne de connexion par la chaîne de connexion appelée **DefaultFullSharedAccessSignature** que vous avez obtenue dans la section « Configuration de votre Notification Hub ».

    > [WACOM.NOTE]Assurez-vous que vous utilisez la chaîne de connexion avec l'accès **total**, pas avec l'accès **Écouter**. La chaîne d'accès en écoute seule ne dispose pas des autorisations pour envoyer des notifications.

6.  Ajoutez les lignes suivantes à la méthode **Main** :

         SendNotificationAsync();
         Console.ReadLine();

7.  L'application console étant définie en tant que projet de démarrage, appuyez sur la touche **F5** pour exécuter l'application.

    ![][19]

    Vous recevrez une notification toast sur tous les appareils enregistrés. En cliquant sur la bannière toast ou en appuyant dessus, l'application se charge.

Vous trouverez toutes les charges utiles prises en charge dans les rubriques du [catalogue toast][], du [catalogue de vignettes][] et de la [vue d'ensemble des badges][] sur MSDN.

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Windows. Pour cibler certains utilisateurs, reportez-vous au didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs][]. Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles][]. Pour en savoir plus sur l'utilisation de Notification Hubs, consultez la page [Recommandations relatives à Notification Hubs][].

  [Windows universel]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows universel"
  [Windows Phone]: /fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /fr-fr/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /fr-fr/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [1]: /fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/
  [Inscription de votre application pour les notifications Push]: #register
  [Configuration de votre concentrateur de notification]: #configure-hub
  [Connexion de votre application au concentrateur de notification]: #connecting-app
  [Envoi de notifications à partir de votre serveur principal]: #send
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
  [2]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
  [3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
  [4]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
  [5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
  [6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
  [7]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
  [8]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
  [10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
  [11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
  [12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
  [13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
  [14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
  [15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png
  [package WindowsAzure.Messaging.Managed NuGet]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
  [17]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
  [interface REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx
  [.NET backend]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Java]: /fr-fr/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /fr-fr/documentation/articles/notification-hubs-php-backend-how-to/
  [18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
  [package NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
  [catalogue toast]: http://msdn.microsoft.com/fr-fr/library/windows/apps/hh761494.aspx
  [catalogue de vignettes]: http://msdn.microsoft.com/fr-fr/library/windows/apps/hh761491.aspx
  [vue d'ensemble des badges]: http://msdn.microsoft.com/fr-fr/library/windows/apps/hh779719.aspx
  [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
  [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet
  [Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/fr-fr/library/jj927170.aspx
