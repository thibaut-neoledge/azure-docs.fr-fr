<properties pageTitle="Get started with push notifications (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main des notifications Push dans Mobile Services
=========================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")

[.NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ ".NET") | [JavaScript](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push/ "JavaScript")

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application Windows Phone 8. Dans ce didacticiel, vous ajoutez des notifications Push à l'aide du Service de notifications Push Microsoft (MPNS) au projet de démarrage rapide. Une fois que vous avez terminé, votre service mobile envoie une notification Push à chaque fois qu'un enregistrement est inséré.

> [WACOM.NOTE]Mobile Services s'intègre désormais à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et la mise à l'échelle. Actuellement, cette fonctionnalité intégrée est uniquement disponible en tant que version préliminaire. Pour plus d'informations, consultez cette version de [Prise en main des notifications Push](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

1.  [Création de la table Registrations](#create-table)
2.  [Ajout de notifications Push à l'application](#add-push)
3.  [Mise à jour des scripts pour l'envoi de notifications Push](#update-scripts)
4.  [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel requiert [Visual Studio 2012 Express pour Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374) ou une version ultérieure.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer de didacticiel, vous devez effectuer la [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-wp8).

> [WACOM.NOTE]Lorsque vous envoyez plus de 500 messages par utilisateur par jour, vous devez plutôt utiliser Notification Hubs. Pour plus d'informations, consultez la page [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/).

Création d'une table
--------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

Ajout de notifications PushAjout de notifications Push à l'application
----------------------------------------------------------------------

1.  Dans Visual Studio, ouvrez le fichier projet MainPage.xaml.cs et ajoutez le code suivant qui crée une classe **Registrations** :

         public class Registrations
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "handle")]
             public string Handle { get; set; }
         }

    La propriété Handle est utilisée pour stocker l'URI de canal.

2.  Ouvrez le fichier App.xaml.cs et ajoutez l'instruction using suivante :

         using Microsoft.Phone.Notification;

3.  Ajoutez le code suivant à App.xaml.cs :

         public static HttpNotificationChannel CurrentChannel { get; private set; }

         private void AcquirePushChannel()
         {
             CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

             if (CurrentChannel == null)
             {
                 CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                 CurrentChannel.Open();
                 CurrentChannel.BindToShellTile();
             }
                      
            IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
            var registration = new Registrations { Handle = CurrentChannel.Uri };
            await registrationsTable.InsertAsync(registration);
         }

	Ce code acquiert et stocke un canal pour un abonnement aux notifications Push et le lie à la vignette par défaut de l'application.

    **Remarque**

    Dans ce didacticiel, le service mobile envoie une notification par vignette retournée à l'appareil. Lorsque vous envoyez une notification toast, vous devez appeler la méthode **BindToShellToast** sur le canal. Pour la prise en charge des notifications toast ainsi que des notifications par vignette, appelez **BindToShellTile** et **BindToShellToast**

4.  En haut du gestionnaire d'événements **Application\_Launching** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **AcquirePushChannel** :

		AcquirePushChannel();

	Cela garantit l'initialisation de la propriété **CurrentChannel** à chaque lancement de l'application.

5.  Dans l'Explorateur de solutions, développez **Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l'onglet **Fonctionnalités** et veillez à ce que la fonctionnalité **ID\_\_*CAP*\_\_PUSH\_NOTIFICATION** soit activée.

	![](./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png) 

	Cela permet de s'assurer que votre application peut recevoir des notifications Push.

Mise à jour du script d'insertionMise à jour des scripts d'insertion inscrits dans le portail de gestion
--------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**.

	![][10]

2.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

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
                            push.mpns.sendFlipTile(registration.uri, {
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

    Ce script de fonction insert envoie une notification Push (avec le texte de l'élément inséré) à tous les canaux stockés dans la table **Registrations**.

Test de l'applicationTest des notifications Push dans l'application
-------------------------------------------------------------------

1.  Dans Visual Studio, sélectionnez **Déployer la solution** dans le menu **Générer**.

2.  Dans l'émulateur, balayez l'écran vers la gauche pour afficher la liste des applications installées et rechercher la nouvelle application **TodoList**.

3.  Appuyez de façon prolongée sur l'icône de l'application, puis sélectionnez **épingler sur l'écran d'accueil** dans le menu contextuel.

	![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png) 
	
	Cela ajoute une vignette nommée **TodoList** au menu Démarrer.

4.  Appuyez sur la vignette nommée **TodoList** pour lancer l'application.

	![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png)

5.  Dans l'application, entrez le texte « hello push » dans la zone de texte, puis cliquez sur **Enregistrer**.

	![][4]

	Cela envoie une requête insert au service mobile pour stocker l'élément ajouté.

6.  Appuyez sur le bouton **Démarrer** pour revenir au menu Démarrer.

	![](./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png) 

	Notez que l'application a reçu la notification Push et que le titre de la vignette est maintenant **hello push**.

Étapes suivantes
----------------

Ce didacticiel présente la fonctionnalité de notification Push de base fournie par Mobile Services. Si votre application requiert des fonctionnalités plus avancées, comme l'envoi de notifications interplateforme, le routage basé sur l'abonnement ou des volumes très importants, envisagez l'utilisation d'Azure Notification Hubs avec votre service mobile. Pour plus d'informations, consultez l'une des rubriques Notification Hubs suivantes :

-   [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8/)
    En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

-   [Présentation de Notification Hubs](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)
    En savoir plus sur la création et l'envoi de notifications Push aux utilisateurs sur plusieurs plateformes.

-   [Envoi de notifications aux abonnés](/en-us/manage/services/notification-hubs/breaking-news-wp8/)
    En savoir plus sur l'enregistrement des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-wp8)
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-wp8)
    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    En savoir plus sur l'utilisation de Mobile Services avec .NET.

<!-- Anchors. -->
[Create the Registrations table]: #create-table
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-wp8
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure Management Portal]: https://manage.windowsazure.com/
[mpns object]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library/
[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-wp8/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/

