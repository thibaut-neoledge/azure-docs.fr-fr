<properties pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Prise en main des notifications Push dans Mobile Services (Push hérité)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store&nbsp;C#" >Windows Store&nbsp;C#</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/fr-fr/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/fr-fr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
    <a href="/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript backend" class="current">JavaScript backend</a>
</div>

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Windows Phone 8.
Dans ce didacticiel, vous allez ajouter des notifications Push utilisant les services de notifications Push Microsoft.au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

> [WACOM.NOTE]Cette rubrique prend en charge les services mobiles *existants* qui n'ont *pas encore été mis à niveau* pour utiliser l'intégration de Notification Hubs. Lorsque vous créez un *nouveau* service mobile, cette fonctionnalité intégrée est automatiquement activée. Pour les nouveaux services mobiles, consultez la rubrique [Prise en main des notifications Push][].
>
> Mobile Services s'intègre à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et la mise à l'échelle améliorée. *Vous devez mettre à niveau vos services mobiles existants pour utiliser Notification Hubs lorsque cela est possible*. Une fois la mise à niveau effectuée, consultez cette version de la [Prise en main des notifications Push][].

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Création de la table Registrations][]
2.  [Ajout de notifications Push à l'application][]
3.  [Mise à jour des scripts pour l'envoi de notifications Push][]
4.  [Insertion de données pour recevoir des notifications][]

Ce didacticiel requiert [Visual Studio 2012 Express pour Windows Phone][] ou une version ultérieure.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services][].

> [WACOM.NOTE]Lorsque vous envoyez plus de 500 messages par utilisateur par jour, vous devez plutôt utiliser Notification Hubs. Pour plus d'informations, consultez la page [Prise en main de Notification Hubs][].

## <a name="create-table"></a>Création d'une table

[WACOM.INCLUDE [mobile-services-create-new-push-table][]]

## <a name="add-push"></a><span class="short-header">Ajout de notifications Push</span>Ajout de notifications Push à l'application

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
    <p>Dans ce didacticiel, le service mobile envoie une notification par vignette retourn&eacute;e &agrave; l'appareil. Lorsque vous envoyez une notification toast, vous devez appeler la m&eacute;thode <strong>BindToShellToast</strong> sur le canal. Pour la prise en charge des notifications toast ainsi que des notifications par vignette, appelez <strong>BindToShellTile</strong> et <strong>BindToShellToast</strong> </p>
</div>

4.  En haut du gestionnaire d'événements **Application\_Launching** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **AcquirePushChannel** :

        AcquirePushChannel();

    Cela garantit que la propriété **CurrentChannel** est initialisée à chaque lancement de l'application.

5.  Dans l'Explorateur de solutions, développez **Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l'onglet **Fonctionnalités** et veillez à ce que la fonctionnalité **ID___CAP___PUSH_NOTIFICATION** soit cochée.

    ![][]

    Cela permet de s'assurer que votre application peut recevoir des notifications Push.

## <a name="update-scripts"></a><span class="short-header">Mise à jour du script d'insertion</span>Mise à jour des scripts d'insertion inscrits dans le portail de gestion

[WACOM.INCLUDE [mobile-services-update-registrations-script][]]

1.  Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**.

    ![][1]

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

## <a name="test"></a><span class="short-header">Test de l'application</span>Test des notifications Push dans l'application

1.  Dans Visual Studio, sélectionnez **Déployer la solution** dans le menu **Générer**.

2.  Dans l'émulateur, balayez l'écran vers la gauche pour afficher la liste des applications installées et rechercher la nouvelle application **TodoList**.

3.  Appuyez de façon prolongée sur l'icône de l'application, puis sélectionnez **épingler sur l'écran d'accueil** dans le menu contextuel.

    ![][2]

    Cela ajoute une vignette nommée **TodoList** au menu Démarrer.

4.  Appuyez sur la vignette nommée **TodoList** pour lancer l'application.

    ![][3]

5.  Dans l'application, entrez le texte « hello push » dans la zone de texte, puis cliquez sur **Enregistrer**.

    ![][4]

    Cela envoie une requête insert au service mobile pour stocker l'élément ajouté.

6.  Appuyez sur le bouton **Démarrer** pour revenir au menu Démarrer.

    ![][5]

    Notez que l'application a reçu la notification Push et que le titre de la vignette est maintenant **hello push**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel présente la fonctionnalité de notification Push de base fournie par Mobile Services. Si votre application requiert des fonctionnalités plus avancées, comme l'envoi de notifications interplateforme, le routage basé sur l'abonnement ou des volumes très importants, envisagez l'utilisation d'Azure Notification Hubs avec votre service mobile. Pour plus d'informations, consultez l'une des rubriques Notification Hubs suivantes :

-   [Prise en main de Notification Hubs][6]

    En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

-   [Présentation de Notification Hubs][]

    En savoir plus sur la création et l'envoi de notifications Push aux utilisateurs sur plusieurs plateformes.

-   [Envoi de notifications aux abonnés][]

    En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données][]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services .NET][]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [.NET backend]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push/ "JavaScript backend"
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Création de la table Registrations]: #create-table
  [Ajout de notifications Push à l'application]: #add-push
  [Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
  [Insertion de données pour recevoir des notifications]: #test
  [Visual Studio 2012 Express pour Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-wp8
  [Prise en main de Notification Hubs]: /fr-fr/manage/services/notification-hubs/getting-started-windows-dotnet/
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  []: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
  [3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
  [6]: /fr-fr/manage/services/notification-hubs/get-started-notification-hubs-wp8/
  [Présentation de Notification Hubs]: /fr-fr/develop/net/how-to-guides/service-bus-notification-hubs/
  [Envoi de notifications aux abonnés]: /fr-fr/manage/services/notification-hubs/breaking-news-wp8/
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-wp8
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-wp8
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library/
