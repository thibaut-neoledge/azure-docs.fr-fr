<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Prise en main des notifications Push dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][]]

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Silverlight Windows Phone. Dans ce didacticiel, vous allez activer des notifications Push dans le projet de démarrage rapide à l'aide des concentrateurs de notification Azure. Une fois la procédure terminée, votre service mobile envoie une notification Push à l'aide des concentrateurs de notification chaque fois qu'un enregistrement est inséré. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

> [WACOM.NOTE]Ce didacticiel présente l'intégration de Mobile Services à Notification Hubs, ce qui correspond à la façon dont vous envoyez des notifications Push depuis votre service mobile. Si vous utilisez un service mobile plus ancien qui utilise un push hérité et qui n'a pas encore été mis à jour pour utiliser Notification Hubs, *il est recommandé d'effectuer une mise à niveau* dans le cadre de ce didacticiel. Si vous décidez de ne pas effectuer de mise à niveau maintenant, vous devez suivre cette version du didacticiel : [Prise en main des notifications Push (hérité)][].

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Mise à jour de l'application pour l'inscription aux notifications][]
2.  [Mise à jour des scripts serveur pour l'envoi de notifications Push][]
3.  [Insertion de données pour recevoir des notifications Push][]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services][] ou [Prise en main des données][] pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l’Assistant Ajouter une notification Push crée cette connexion pour vous.

> [WACOM.NOTE]Pour envoyer des notifications Push à une application Windows Phone 8.1 Store, suivez la version [application Windows Store][] de ce didacticiel.

## <span id="update-app"></span></a> Mise à jour de l'application pour l'inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1.  Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.Phone.Notification;

2.  Ajoutez le code suivant à App.xaml.cs :

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

    Ce code récupère l'URI de canal ChannelURI pour l'application auprès du Service de notifications Push Microsoft (MPNS) utilisés par « Silverlight » Windows Phone 8.x, puis il inscrit ce ChannelURI aux notifications Push.

    > [WACOM.NOTE] Dans ce didacticiel, le service mobile envoie une notification toast à l'appareil. Lorsque vous envoyez une notification par vignette, vous devez appeler la méthode **BindToShellTile** sur le canal.

3.  En haut du gestionnaire d'événements **Application\_Launching** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **AcquirePushChannel** :

        AcquirePushChannel();

    Cela permet de s'assurer que l'inscription est demandée à chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude.

4.  Appuyez sur **F5** pour lancer l'application. Une boîte de dialogue s'affiche avec la clé d'inscription.

5.  Dans l'Explorateur de solutions, développez **Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l'onglet **Fonctionnalités** et veillez à ce que la fonctionnalité **ID___CAP___PUSH_NOTIFICATION** soit cochée.

    ![][]

    Cela permet de s'assurer que votre application peut déclencher des notifications toast.

## <span id="update-scripts"></span></a> Mise à jour des scripts serveur pour l'envoi de notifications Push

Enfin, vous devez mettre à jour le script inscrit dans l'opération d'insertion sur la table TodoItem pour envoyer les notifications.

1.  Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**.

    ![][1]

2.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

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

3.  Cliquez sur l'onglet **Push**, cochez **Activez des notifications push non authentifiées**, puis cliquez sur **Enregistrer**.

    > [WACOM.NOTE]Si vous suivez ce didacticiel en utilisant un ancien service mobile, vous verrez peut-être un lien au bas de l'onglet **Push** indiquant **Activer la transmission push améliorée**. Cliquez dessus pour mettre à niveau votre service mobile et l'intégrer à Notification Hubs. Cette modification est irréversible. Pour plus d'informations sur l'activation de notifications Push améliorées dans un service mobile de production, consultez [ce guide][].

    ![][2]

    Le service mobile peut ainsi se connecter à MPNS en mode non authentifié pour envoyer les notifications Push.

    > [WACOM.NOTE] Ce didacticiel utilise MPNS en mode non authentifié. Dans ce mode, MPNS limite le nombre de notifications à envoyer à un canal d'appareil. Pour supprimer cette restriction, vous devez générer et télécharger un certificat en cliquant sur **Télécharger** et en sélectionnant le certificat. Pour plus d'informations sur la génération du certificat, consultez la rubrique [Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone][].

## <span id="test"></span></a>Test des notifications Push dans votre application

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

    > [WACOM.NOTE] Vous pouvez rencontrer une exception « 401 Unauthorized RegistrationAuthorizationException » lors de tests sur l'émulateur Windows Phone. Cela peut se produire pendant l'appel de `RegisterNativeAsync()` en raison de la manière dont l'émulateur Windows Phone synchronise son horloge avec le PC hôte. Un jeton de sécurité pourrait être rejeté. Pour résoudre ce problème, il vous suffit de paramétrer manuellement l'horloge dans l'émulateur avant les tests.

2.  Dans l'application, entrez le texte « hello push » dans la zone de texte, cliquez sur **Enregistrer**, puis cliquez immédiatement sur le bouton Démarrer ou sur le bouton Précédent pour quitter l'application.

    ![][3]

    Cela envoie une requête insert au service mobile pour stocker l'élément ajouté. Notez que l'appareil reçoit une notification toast qui indique **hello push**.

    ![][4]

    > [WACOM.NOTE]Vous ne recevrez pas cette notification pendant que vous vous trouvez encore dans l'application. Pour recevoir une notification toast pendant que l'application est active, vous devez gérer l'événement [ShellToastNotificationReceived][].

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs pour envoyer des notifications Push. Nous vous invitons maintenant à suivre l'un des didacticiels suivants :

-   [Envoi de notifications Push à des utilisateurs authentifiés][]

    En savoir plus sur l'utilisation de balises pour envoyer des notifications Push depuis un service mobile uniquement à un utilisateur authentifié.

-   [Envoi de notifications diffusées aux abonnés][]

    En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

<!---+ [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

-   [Prise en main des données][]

    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][]

    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Guide de fonctionnement Mobile Services .NET][]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'implémentation de logique métier dans votre service mobile.

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Prise en main des notifications Push (hérité)]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push/
  [Mise à jour de l'application pour l'inscription aux notifications]: #update-app
  [Mise à jour des scripts serveur pour l'envoi de notifications Push]: #update-scripts
  [Insertion de données pour recevoir des notifications Push]: #test
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data
  [application Windows Store]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  []: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
  [ce guide]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Configuration d'un service Web authentifié afin d'envoyer des notifications Push pour Windows Phone]: http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/fr-fr/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx
  [Envoi de notifications Push à des utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/
  [Envoi de notifications diffusées aux abonnés]: /fr-fr/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
