<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc"  solutions="" writer="wesmc" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Prise en main des notifications Push dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Cette rubrique montre comment utiliser Azure Mobile Services avec un service principal .NET pour envoyer des notifications Push à une application Windows Store Silverlight 8. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide d'Azure Notification Hubs. Une fois la procédure terminée, votre service mobile envoie une notification Push à l'aide des concentrateurs de notification chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Mise à jour de l'application pour l'inscription aux notifications][Mise à jour de l'application pour l'inscription aux notifications]
2.  [Mise à jour du serveur pour l'envoi de notifications Push][Mise à jour du serveur pour l'envoi de notifications Push]
3.  [Activation des notifications push pour les tests en local][Activation des notifications push pour les tests en local]
4.  [Insertion de données pour recevoir des notifications Push][Insertion de données pour recevoir des notifications Push]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données] pour connecter votre projet au service mobile.

> [WACOM.NOTE]Ce didacticiel concerne les applications Windows Phone 8.1 « Silverlight ». Si vous créez au contraire une application Windows Phone 8.1 Store, consultez la version de l'[application Windows Store][application Windows Store] de ce didacticiel. Pour plus d'informations sur les applications Windows Phone Silverlight et leurs différences par rapport aux applications Windows Phone Store, consultez la section [Applications Windows Phone Silverlight 8.1][Applications Windows Phone Silverlight 8.1].

## <span id="update-app"></span></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1.  Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.Phone.Notification;

2.  Ajoutez la méthode suivante `AcquirePushChannel` à la classe `App` :

        public static HttpNotificationChannel CurrentChannel { get; private set; }  

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

4.  Appuyez sur **F5** pour lancer l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.

5.  Dans Visual Studio, ouvrez le fichier Package.appxmanifest et vérifiez que **Compatible toast** est défini sur **Oui** dans l'onglet **Interface utilisateur de l'application**.

    ![][0]

    Cela permet de s'assurer que votre application peut déclencher des notifications toast.

## <span id="update-server"></span></a>Mise à jour du serveur pour l'envoi de notifications push

1.  Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de service mobile. Ouvrez TodoItemController.cs et mettez à jour la définition de la méthode `PostTodoItem` avec le code suivant :

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>" + item.Text + "</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Ce code envoie une notification Push (avec le texte de l'élément inséré) après l'insertion d'un élément ToDo. En cas d'erreur, le code ajoute une entrée dans le journal des erreurs consultable à partir de l'onglet **Journaux** du service mobile sur le portail de gestion.

2.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

3.  Cliquez sur l'onglet **Push**, cochez **Activez des notifications push non authentifiées**, puis cliquez sur **Enregistrer**.

    ![][1]

    > [WACOM.NOTE] Ce didacticiel utilise MPNS en mode non authentifié. Dans ce mode, MPNS limite le nombre de notifications à envoyer à un canal d'appareil. Pour supprimer cette restriction, vous devez générer et télécharger un certificat en cliquant sur **Télécharger** et en sélectionnant le certificat. Pour plus d'informations sur la génération du certificat, consultez la rubrique [Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone][Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone].

Le service mobile peut ainsi se connecter à MPNS en mode non authentifié pour envoyer les notifications Push.

## <span id="local-testing"></span></a>Activation des notifications push pour les tests en local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a> Test des notifications Push dans votre application

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

    > [WACOM.NOTE] Vous pouvez rencontrer l'exception d'autorisation d'inscription « 401 non autorisé » lors du test sur l'émulateur Windows Phone. Ce problème peut se produire lors de l'appel `RegisterNativeAsync()` en raison de la manière dont l'émulateur Windows Phone synchronise son horloge avec le PC hôte. Ceci peut entraîner le rejet d'un jeton de sécurité. Pour résoudre ce problème, il vous suffit de régler manuellement l'horloge de l'émulateur avant le test.

2.  Dans l'application, entrez le texte « hello push » dans la zone de texte, cliquez sur **Enregistrer**, puis cliquez immédiatement sur le bouton Démarrer ou Retour pour quitter l'application.

    ![][2]

    Cela envoie une requête insert au service mobile pour stocker l'élément ajouté. Notez que l'appareil reçoit une notification toast qui indique **hello push**.

    ![][3]

    > [WACOM.NOTE]Vous ne recevrez pas la notification tant que vous êtes dans l'application. Pour recevoir une notification toast pendant que l'application est active, vous devez gérer l'événement [ShellToastNotificationReceived][ShellToastNotificationReceived].

## <a name="next-steps">Étapes suivantes</a>

Ce didacticiel a présenté les bases de l'activation d'une application Windows Phone pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Envisagez ensuite de suivre le didacticiel suivant, [Envoyer des notifications Push aux utilisateurs authentifiés][Envoyer des notifications Push aux utilisateurs authentifiés], qui montre comment utiliser les balises pour envoyer des notifications Push à partir d'un service mobile uniquement à un utilisateur authentifié.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in. -->

Pour plus d'informations sur les rubriques Mobile Services et Notifications Hubs suivantes :

-   [Prise en main des données][Prise en main des données]

    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][Présentation de Notification Hubs]

    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Guide de fonctionnement Mobile Services .NET][Guide de fonctionnement Mobile Services .NET]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Mise à jour de l'application pour l'inscription aux notifications]: #update-app
  [Mise à jour du serveur pour l'envoi de notifications Push]: #update-server
  [Activation des notifications push pour les tests en local]: #local-testing
  [Insertion de données pour recevoir des notifications Push]: #test
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [application Windows Store]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Applications Windows Phone Silverlight 8.1]: http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/dn642082(v=vs.105).aspx
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone]: http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx
  [Envoyer des notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library
