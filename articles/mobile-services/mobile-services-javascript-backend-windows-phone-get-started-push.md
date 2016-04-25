<properties
	pageTitle="Ajouter des notifications Push à votre application Mobile Services (Windows Phone) | Microsoft Azure"
	description="Découvrez comment utiliser Azure Mobile Services et Notification Hubs pour envoyer des notifications Push à votre application Windows Phone."
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="glenga"/>


# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Vue d'ensemble

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Silverlight Windows Phone. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide d'Azure Notification Hubs. Une fois la procédure terminée, votre service mobile envoie une notification Push à l'aide des hubs de notification chaque fois qu'un enregistrement est inséré. Le hub de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

Ce didacticiel est basé sur l'exemple d'application TodoList. Avant de commencer ce didacticiel, vous devez suivre la rubrique [Ajout de Mobile Services à une application existante] pour connecter votre projet au service mobile. Quand un service mobile n'est pas connecté, l'Assistant Ajouter une notification Push peut créer cette connexion pour vous.

>[AZURE.NOTE] Pour envoyer des notifications Push à une application Windows Phone 8.1 Store, suivez la version [application Windows Store](mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md) de ce didacticiel.

##<a id="update-app"></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1. Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.Phone.Notification;

3. Ajoutez le code suivant à App.xaml.cs :

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
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    Ce code récupère l’URI de canal ChannelURI pour l’application auprès du Service de notifications Push Microsoft (MPNS) utilisés par « Silverlight » Windows Phone 8.x, puis il inscrit ce ChannelURI aux notifications Push.

	>[AZURE.NOTE]Dans ce didacticiel, le service mobile envoie une notification toast à l’appareil. Lorsque vous envoyez une notification par vignette, vous devez appeler la méthode **BindToShellTile** sur le canal.

4. En haut du gestionnaire d'événements **Application\_Launching** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **AcquirePushChannel** :

        AcquirePushChannel();

	Cela permet de s’assurer que l’inscription est demandée à chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude.

5. Appuyez sur la touche **F5** pour exécuter l'application. Une boîte de dialogue s’affiche avec la clé d’inscription.

6.	Dans l'Explorateur de solutions, développez **Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l'onglet **Fonctionnalités** et veillez à ce que la fonctionnalité **ID\_\_\_CAP\_\_\_PUSH\_NOTIFICATION** soit activée.

   	![Activer les notifications dans VS](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)

   	Cela permet de s’assurer que votre application peut déclencher des notifications toast.

##<a id="update-scripts"></a> Mise à jour des scripts serveur pour l'envoi de notifications Push

Enfin, vous devez mettre à jour le script inscrit dans l'opération d'insertion sur la table TodoItem pour envoyer les notifications.

1. Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**.

2. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

		function insert(item, user, request) {
		// Define a payload for the Windows Phone toast notification.
		var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text +
		    '</wp:Text2></wp:Toast></wp:Notification>';

		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		    	push.mpns.send(null, payload, 'toast', 22, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse);
						request.respond();
		                },              
		                error: function (pushResponse) {
		                    console.log("Error Sending push:", pushResponse);
							request.respond(500, { error: pushResponse });
		                    }
		                });
		            }
		        });      
		}

	À l'issue de l'insertion, ce script d'insertion envoie une notification Push (avec le texte de l'élément inséré) à toutes les inscriptions d'applications Windows Phone.

3. Cliquez sur l'onglet **Push**, cochez **Activez des notifications push non authentifiées**, puis cliquez sur **Enregistrer**.

	Le service mobile peut ainsi se connecter à MPNS en mode non authentifié pour envoyer les notifications Push.

	>[AZURE.NOTE]Ce didacticiel utilise MPNS en mode non authentifié. Dans ce mode, MPNS limite le nombre de notifications à envoyer à un canal d’appareil. Pour supprimer cette restriction, vous devez générer et télécharger un certificat en cliquant sur **Télécharger** et en sélectionnant le certificat. Pour plus d'informations sur la génération du certificat, consultez la rubrique [Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone].

##<a id="test"></a> Test des notifications Push dans votre application

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l’application.

    >[AZURE.NOTE] Vous pouvez rencontrer une exception « 401 Unauthorized RegistrationAuthorizationException » lors de tests sur l’émulateur Windows Phone. Cela peut se produire pendant l'appel de `RegisterNativeAsync()` en raison de la manière dont l'émulateur Windows Phone synchronise son horloge avec le PC hôte. Un jeton de sécurité pourrait être rejeté. Pour résoudre ce problème, il vous suffit de paramétrer manuellement l'horloge dans l'émulateur avant les tests.

5. Dans l'application, entrez le texte « hello push » dans la zone de texte, cliquez sur **Enregistrer**, puis cliquez immédiatement sur le bouton Démarrer ou sur le bouton Précédent pour quitter l'application.

   	![Entrer du texte dans l'application](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png)

  	Cela envoie une requête insert au service mobile pour stocker l'élément ajouté. Notez que l'appareil reçoit une notification toast qui indique **hello push**.

	![Notification toast reçue](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]Vous ne recevrez pas cette notification si vous vous trouvez encore dans l'application. Pour recevoir une notification toast pendant que l'application est active, vous devez gérer l'événement [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx).

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Nous vous invitons maintenant à suivre l'un des didacticiels suivants :

+ [Envoi de notifications diffusées aux abonnés](../notification-hubs/notification-hubs-windows-phone-send-breaking-news.md) <br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

+ [Envoi de notifications indépendantes de toute plateforme aux abonnés](../notification-hubs/notification-hubs-aspnet-cross-platform-notify-users.md) <br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis votre service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.


Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Azure Notification Hubs : instructions relatives au diagnostic](../notification-hubs/notification-hubs-diagnosing.md) <br/>En savoir plus sur la résolution des problèmes liés aux notifications Push.

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

* [Présentation de Notification Hubs] <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Guide de fonctionnement Mobile Services .NET] <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.

* [Référence de script serveur Mobile Services] <br/>En savoir plus sur l'implémentation de logique métier dans votre service mobile.

<!-- Anchors. -->

<!-- Images. -->


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Ajout de Mobile Services à une application existante]: mobile-services-windows-phone-get-started-data.md
[Prise en main de l'authentification]: mobile-services-windows-phone-get-started-users.md

[Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Guide de fonctionnement Mobile Services .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md

[Présentation de Notification Hubs]: ../notification-hubs/notification-hubs-overview.md

<!---HONumber=AcomDC_0413_2016-->