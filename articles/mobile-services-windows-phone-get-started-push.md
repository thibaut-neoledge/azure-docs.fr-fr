<properties pageTitle="Prise en main des notifications Push (push hérité) | Centre de développement mobile" metaKeywords="" description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Windows Phone (push hérité)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="glenga" />


# Prise en main des notifications Push dans Mobile Services (Push hérité)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" >Windows Store C#</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/fr-fr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
	<a href="/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET backend">Serveur principal .NET<a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript backend" class="current">Serveur principal JavaScript</a>
</div>

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application Windows Phone 8. 
Dans ce didacticiel, vous ajoutez des notifications Push à l'aide du Service de notifications Push Microsoft (MPNS) au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

>[WACOM.NOTE]Cette rubrique prend en charge les services mobiles <em>existants</em> qui <em>n'ont pas encore été mis à niveau</em> pour utiliser l'intégration à Notification Hubs. Lorsque vous créez un <em>nouveau</em> service mobile, cette fonctionnalité intégrée est automatiquement activée. Pour les nouveaux services mobiles, consultez la rubrique [Prise en main des notifications Push](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).
>
>Mobile Services s'intègre à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et la mise à l'échelle améliorée. <em>Vous devez mettre à niveau vos services mobiles existants pour utiliser Notification Hubs lorsque cela est possible</em>. Une fois la mise à niveau effectuée, consultez cette version de la [Prise en main des notifications Push](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Création de la table Registrations]
2. [Ajout de notifications Push à l'application]
3. [Mise à jour des scripts pour l'envoi de notifications Push]
4. [Insertion de données pour recevoir des notifications]

Ce didacticiel requiert [Visual Studio 2012 Express pour Windows Phone] ou une version ultérieure.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services]. 

   >[WACOM.NOTE]Si vous envoyez plus de 500 messages par utilisateur et par jour, vous devez plutôt utiliser Notification Hubs. Pour plus d'informations, consultez la page <a href="/fr-fr/manage/services/notification-hubs/getting-started-windows-dotnet/">Prise en main de Notification Hubs</a>.

## <a name="create-table"></a>Création d'une table

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a>Ajout de notifications Push à votre application</h2>
		
1. Dans Visual Studio, ouvrez le fichier projet MainPage.xaml.cs et ajoutez le code suivant qui crée une classe **Registrations** :

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	La propriété Handle est utilisée pour stocker l'URI de canal.

2. Ouvrez le fichier App.xaml.cs et ajoutez l'instruction using suivante :

        using Microsoft.Phone.Notification;

3. Ajoutez le code suivant à App.xaml.cs :
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

		private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
	       await registrationsTable.InsertAsync(registration);
        }

   	Ce code acquiert et stocke un canal pour un abonnement aux notifications Push et le lie à la vignette par défaut de l'application.

	<div class="dev-callout"><b>Remarque</b>
		<p>Dans ce didacticiel, le service mobile envoie une notification par vignette retournée à l'appareil. Lorsque vous envoyez une notification toast, vous devez appeler la méthode <strong>BindToShellToast</strong> sur le canal. Pour la prise en charge des notifications toast, ainsi que des notifications par vignette, appelez <strong>BindToShellTile</strong> et <strong>BindToShellToast</strong></p>.
	</div>
    
4. En haut du gestionnaire d'événements **Application_Launching** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **AcquirePushChannel** :

        AcquirePushChannel();

   	Cela garantit que la propriété **CurrentChannel** est initialisée à chaque lancement de l'application.


5.	Dans l'Explorateur de solutions, développez **Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l'onglet **Fonctionnalités** et veillez à ce que la fonctionnalité **ID___CAP___PUSH_NOTIFICATION** soit activée.

   	![][1]

   	Cela permet de s'assurer que votre application peut recevoir des notifications Push.

<h2><a name="update-scripts"></a>Mise à jour des scripts d'insertion inscrits dans le portail de gestion</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Cliquez sur **TodoItem**, sur **Script**, puis sélectionnez **Insérer**. 

   	![][10]

3. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.mpns.sendFlipTile(registration.handle, {
                        	    title: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }

    Ce script d'insertion envoie une notification Push (avec le texte de l'élément inséré) à tous les canaux stockés dans la table **Registrations**.

<h2><a name="test"></a>Test des notifications Push dans votre application</h2>

1. Dans Visual Studio, sélectionnez **Déployer la solution** dans le menu **Générer**.

2. Dans l'émulateur, balayez l'écran vers la gauche pour afficher la liste des applications installées et rechercher la nouvelle application **TodoList**.

3. Appuyez de façon prolongée sur l'icône de l'application, puis sélectionnez **épingler sur l'écran d'accueil** dans le menu contextuel.

  	![][2]

  	Cela ajoute une vignette nommée **TodoList** au menu Démarrer.

4. Appuyez sur la vignette nommée **TodoList** pour lancer l'application. 

  	![][3]

5. Dans l'application, entrez le texte " hello push " dans la zone de texte, puis cliquez sur **Enregistrer**.

   	![][4]

  	Cela envoie une requête insert au service mobile pour stocker l'élément ajouté.

6. Appuyez sur le bouton **Démarrer** pour revenir au menu Démarrer. 

  	![][5]

  	Notez que l'application a reçu la notification Push et que le titre de la vignette est maintenant **hello push**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel présente la fonctionnalité de notification Push de base fournie par Mobile Services. Si votre application requiert des fonctionnalités plus avancées, comme l'envoi de notifications interplateforme, le routage basé sur l'abonnement ou des volumes très importants, envisagez l'utilisation d'Azure Notification Hubs avec votre service mobile. Pour plus d'informations, consultez l'une des rubriques Notification Hubs suivantes :

+ [Prise en main de Notification Hubs]
  <br/>En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

+ [Présentation de Notification Hubs]
	<br/>En savoir plus sur la création et l'envoi de notifications Push aux utilisateurs sur plusieurs plateformes.

+ [Envoi de notifications aux abonnés]
	<br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

<!--+ [Send notifications to users]
	<br/>Learn how to send push notifications from a Mobile Service to specific users on any device.

+ [Send cross-platform notifications to users]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Pour plus d'informations sur les rubriques Mobile Services suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec un compte Windows.

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

* [Guide de fonctionnement Mobile Services .NET]
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec .NET. 

<!-- Anchors. -->
[Création de la table Registrations]: #create-table
[Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
[Ajout de notifications Push à l'application]: #add-push
[Insertion de données pour recevoir des notifications]: #test
[Étapes suivantes]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[Kit de développement logiciel (SDK) de Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express pour Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-wp8
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-wp8
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-wp8
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-wp8
[Notifications Push pour les utilisateurs de l'application]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-wp8
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Portail de gestion Azure]: https://manage.windowsazure.com/
[objet mpns]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library/
[Prise en main de Notification Hubs]: /fr-fr/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Présentation de Notification Hubs]: /fr-fr/develop/net/how-to-guides/service-bus-notification-hubs/
[Envoi de notifications aux abonnés]: /fr-fr/manage/services/notification-hubs/breaking-news-wp8/
[Envoi de notifications aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users/
[Envoi de notifications interplateforme aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-xplat-mobile-services/
