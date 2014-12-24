<properties pageTitle="Prise en main des notifications Push (Windows Store) | Centre de développement mobile" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />


# Ajout de notifications Push à votre application Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Silverlight Windows Phone. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide des concentrateurs de notification Azure. Une fois la procédure terminée, votre service mobile envoie une notification Push à l'aide des concentrateurs de notification chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Mise à jour de l'application pour l'inscription aux notifications](#update-app)
2. [Mise à jour des scripts serveur pour l'envoi de notifications Push](#update-scripts)
3. [Insertion de données pour recevoir des notifications Push](#test)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel  [Prise en main de Mobile Services]  ou  [Prise en main des données]  pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l'Assistant Ajouter une notification Push crée cette connexion pour vous. 

>[WACOM.NOTE]Pour envoyer des notifications Push à une application Windows Phone 8.1 Store, suivez la version  [application Windows Store](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push)  de ce didacticiel.

##<a id="update-app"></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1. Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.Phone.Notification;

3. Ajoutez le code suivant à App.xaml.cs :
	
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

    Ce code récupère l'URI de canal ChannelURI pour l'application auprès du Service de notifications Push Microsoft (MPNS) utilisés par " Silverlight " Windows Phone 8.x, puis il inscrit ce ChannelURI aux notifications Push.

	>[WACOM.NOTE]Dans ce didacticiel, le service mobile envoie une notification toast à l'appareil. Lorsque vous envoyez une notification par vignette, vous devez appeler la méthode **BindToShellTile** sur le canal.

4. En haut du gestionnaire d'événements **Application_Launching** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode  **AcquirePushChannel** :

        AcquirePushChannel();

	Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude. 

5. Appuyez sur la touche **F5** pour exécuter l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.
  
6.	Dans l'Explorateur de solutions, développez**Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l'onglet **Fonctionnalités** et veillez à ce que la fonctionnalité  **ID___CAP___PUSH_NOTIFICATION** soit activée.

   	![][1]

   	Cela permet de s'assurer que votre application peut déclencher des notifications toast. 

##<a id="update-scripts"></a> Mise à jour des scripts serveur pour l'envoi de notifications Push

Enfin, vous devez mettre à jour le script inscrit dans l'opération d'insertion sur la table TodoItem pour envoyer les notifications.

1. Cliquez sur **TodoItem**, sur **Script**, puis sélectionnez **Insérer**. 

   	![][10]

2. Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer** :

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

3. Cliquez sur l'onglet **Notifications Push**, cochez **Activez des notifications push non authentifiées**, then click **, puis cliquez sur **.

	>[WACOM.NOTE]Si vous suivez ce didacticiel avec un service mobile plus ancien, vous êtes susceptible de voir un lien dans le bas de l'onglet**Notifications Push**avec le texte **Activer la transmission push améliorée**. Cliquez sur ce lien pour mettre à niveau votre service mobile et l'intégrer avec Notification Hubs. Cette modification est irréversible. Pour plus d'informations sur l'activation des notifications Push améliorées dans un service mobile de production, consultez  <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">ce guide</a>.

	![][11]

	Le service mobile peut ainsi se connecter à MPNS en mode non authentifié pour envoyer les notifications Push.

	>[WACOM.NOTE]Ce didacticiel utilise MPNS en mode non authentifié. Dans ce mode, MPNS limite le nombre de notifications à envoyer à un canal d'appareil. Pour supprimer cette restriction, vous devez générer et télécharger un certificat en cliquant sur**Télécharger** et en sélectionnant le certificat. Pour plus d'informations sur la génération du certificat, consultez la rubrique  [Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone].

##<a id="test"></a> Test des notifications Push dans votre application

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

    >[WACOM.NOTE] Vous pouvez rencontrer une exception " 401 Unauthorized RegistrationAuthorizationException " lors de tests sur l'émulateur Windows Phone. Cela peut se produire pendant l'appel de `RegisterNativeAsync()` en raison de la manière dont l'émulateur Windows Phone synchronise son horloge avec le PC hôte. Un jeton de sécurité pourrait être rejeté. Pour résoudre ce problème, il vous suffit de paramétrer manuellement l'horloge dans l'émulateur avant les tests.

5. Dans l'application, entrez le texte " hello push " dans la zone de texte, cliquez sur **Enregistrer**, puis cliquez immédiatement sur le bouton Démarrer ou sur le bouton Précédent pour quitter l'application.

   	![][4]

  	Cela envoie une requête insert au service mobile pour stocker l'élément ajouté. Notez que l'appareil reçoit une notification toast qui indique **hello push**.

	![][5]

	>[WACOM.NOTE]Vous ne recevrez pas cette notification pendant que vous vous trouvez encore dans l'application. Pour recevoir une notification toast pendant que l'application est active, vous devez gérer l'événement  [ShellToastNotificationReceived](http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx).



## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Nous vous invitons maintenant à suivre l'un des didacticiels suivants :

+ [Envoi de notifications Push aux utilisateurs authentifiés]
	<br/>En savoir plus sur l'utilisation de balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

+ [Envoi de notifications aux abonnés]
	<br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

<!---+ [Send template-based notifications to subscribers]
	<br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.
-->
Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de services mobiles.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents à l'aide de services mobiles.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Guide de fonctionnement Mobile Services .NET]
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec .NET.

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur l'implémentation d'une logique métier dans votre service mobile.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users

[Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone]: http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/ff941099(v=vs.105).aspx

[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/

[Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
[Envoi de notifications aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
[Envoi de notifications basées sur un modèle aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-phone-send-localized-breaking-news/
