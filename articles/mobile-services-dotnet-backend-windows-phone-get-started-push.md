<properties 
	pageTitle="Prise en main des concentrateurs de notification Push à l'aide des services mobiles .NET runtime" 
	description="Découvrez comment utiliser les services mobiles d'exécution Microsoft Azure .Net et Notification Hubs pour envoyer des notifications Push à votre application Windows Phone." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	Writer="wesmc" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Cette rubrique montre comment utiliser Azure Mobile Services avec un service principal .NET pour envoyer des notifications Push à une application Windows Phone Silverlight 8. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide d'Azure Notification Hubs. Une fois la procédure terminée, votre service mobile envoie une notification Push à l'aide des concentrateurs de notification chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push:

1. [Mise à jour de l'application pour l'inscription aux notifications](#update-app)
3. [Mise à jour du serveur pour l'envoi de notifications push](#update-server)
4. [Activation des notifications Push pour les tests en local](#local-testing)
3. [Insertion de données pour recevoir des notifications Push](#test)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez suivre [Ajout de Mobile Services à une application existante] pour connecter votre projet au service mobile.

>[AZURE.NOTE]Ce didacticiel vise les applications Windows Phone 8.1 "Silverlight". Si vous créez au contraire une application Windows Phone 8.1 Store, consultez la version de l'application [Windows Store](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) de ce didacticiel. Pour plus d'informations sur les applications Windows Phone Silverlight et leurs différences par rapport aux applications Windows Phone Store, consultez la section [Applications Windows Phone Silverlight 8.1]. 

##<a id="update-app"></a>Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1. Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez les  `using`instructions :

        using Microsoft.Phone.Notification;

2. Ajoutez la méthode `AcquirePushChannel` suivante à la classe `App` : 

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
    
4. Dans le gestionnaire d'événements `Application_Launching` du fichier App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode `AcquirePushChannel` :

        AcquirePushChannel();

	L'inscription est ainsi demandée chaque fois que l'application est chargée. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude. 

5. Appuyez sur la touche **F5** pour exécuter l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.
  
6. Dans Visual Studio, ouvrez le fichier Package.appxmanifest et vérifiez que **Compatible toast** est défini sur **Oui** dans l'onglet **Interface utilisateur** de l'application.

   	![][1]

   	Cela permet de s'assurer que votre application peut déclencher des notifications toast. 

##<a id="update-server"></a> Mise à jour du serveur pour l'envoi de notifications push

1. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs du projet de service mobile**. Ouvrez TodoItemController.cs et mettez à jour la définition de la méthode  `PostTodoItem` avec le code suivant :  

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

2. Connectez-vous au [portail de gestion Azure], puis cliquez sur **Mobile Services** et sur l'application.

3. Cliquez sur l'onglet **Push**, cochez **Activez des notifications push non authentifiées**, puis cliquez sur **Enregistrer**.

   	![][4]

	>[AZURE.NOTE]Ce didacticiel utilise MPNS en mode non authentifié. Dans ce mode, MPNS limite le nombre de notifications à envoyer à un canal d'appareil. Pour supprimer cette restriction, vous devez générer et télécharger un certificat en cliquant sur <strong>Télécharger</strong> et en sélectionnant le certificat. Pour plus d'informations sur la génération du certificat, consultez la rubrique <a href="http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx">Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone</a>.

Le service mobile peut ainsi se connecter à MPNS en mode non authentifié pour envoyer les notifications Push.

##<a id="local-testing"></a> Activation des notifications Push pour les tests en local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]


##<a id="test"></a>Test des notifications Push dans votre application

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

    >[AZURE.NOTE] Vous pouvez rencontrer une exception " 401 Unauthorized RegistrationAuthorizationException " lors de tests sur l'émulateur Windows Phone. Cela peut se produire pendant l'appel de `RegisterNativeAsync()` en raison de la manière dont l'émulateur Windows Phone synchronise son horloge avec le PC hôte. Un jeton de sécurité pourrait être rejeté. Pour résoudre ce problème, il vous suffit de paramétrer manuellement l'horloge dans l'émulateur avant les tests.

5. Dans l'application, entrez le texte "hello push" dans la zone de texte, cliquez sur **Enregistrer**, puis cliquez immédiatement sur le bouton Démarrer ou sur le bouton Précédent pour quitter l'application.

   	![][2]

  	Cela envoie une requête insert au service mobile pour stocker l'élément ajouté. Notez que l'appareil reçoit une notification toast qui indique **hello push**.

	![][5]

	>[AZURE.NOTE]Vous ne recevrez pas cette notification pendant que vous vous trouvez encore dans l'application. Pour recevoir une notification toast pendant que l'application est active, vous devez gérer l'événement [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx).

## <a name="next-steps">Étapes suivantes</a>

Ce didacticiel a présenté les bases de l'activation d'une application Windows Phone pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

<!--+ [Envoi de notifications Push aux utilisateurs authentifiés]
	<br/>En savoir plus sur l'utilisation de balises pour envoyer des notifications Push depuis un service mobile uniquement à un utilisateur authentifié.

+ [Envoi de notifications diffusées aux abonnés]
	<br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.
-->
Pour plus d'informations sur les rubriques Mobile Services et Notifications Hubs suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenez des conseils sur le dépannage et le débogage des solutions Notification Hubs. 

* [Guide de fonctionnement Mobile Services .NET]
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec .NET

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
[5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png

<!-- URLs. -->
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
[Ajout de Mobile Services à une application existante]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users

[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/

[Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
[Envoi de notifications diffusées aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[Envoi de notifications basées sur un modèle aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/


[Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library
[Applications Windows Phone Silverlight 8.1]: http://msdn.microsoft.com/library/windowsphone/develop/dn642082(v=vs.105).aspx
[Portail de gestion Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
