<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="push notifications c#" description="Learn how to use push notifications with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services using Visual Studio 2012" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Prise en main des notifications Push dans Mobile Services avec Visual Studio 2012

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Windows Store&nbsp;C#" class="current">Windows Store&nbsp;C#</a>
    <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
    <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a>
    <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Windows Store.
Dans ce didacticiel, vous allez ajouter des notifications Push utilisant les services de notifications Windows (WNS) au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

<div class="dev-callout"><b>Remarque</b>
    <p>Ce didacticiel permet d'ajouter des notifications Push &agrave; une application Windows Store cr&eacute;&eacute;e dans Visual Studio&nbsp;2012. Visual Studio&nbsp;2013 int&egrave;gre de nouvelles fonctionnalit&eacute;s qui facilitent la configuration des notifications Push dans votre application Windows Store en utilisant Mobile Services. Pour la version Visual Studio&nbsp;2013, consultez la page <a href="/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet">Prise en main des notifications Push</a>.</p>
</div>

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push et configuration de Mobile Services][]
2.  [Création de la table Registrations][]
3.  [Ajout de notifications Push à l'application][]
4.  [Mise à jour des scripts pour l'envoi de notifications Push][]
5.  [Insertion de données pour recevoir des notifications][]

Ce didacticiel requiert les éléments suivants :

-   Microsoft Visual Studio 2012 Express pour Windows 8
-   un compte Windows Store actif.

Ce didacticiel s'appuie sur le didacticiel [Prise en main des données][]. Avant de commencer ce didacticiel, vous devez d'abord suivre intégralement [celui-ci][Prise en main des données] :

## <a name="register"></a><span class="short-header">Inscription de votre application</span>Inscription de votre application pour le Windows Store

Pour pouvoir envoyer des notifications Push vers des applications Windows Store à partir de Mobile Services, vous devez soumettre votre application au Windows Store. Vous devez ensuite configurer votre service mobile pour l'intégrer à WNS.

[WACOM.INCLUDE [mobile-services-register-windows-store-app][]]

Votre service mobile et votre application sont dès lors configurés pour fonctionner avec WNS. La prochaine étape consiste à créer une table pour stocker les inscriptions.

## <a name="create-table"></a>Création d'une table

[WACOM.INCLUDE [mobile-services-create-new-push-table][]]

## <a name="add-push"></a><span class="short-header">Ajout de notifications Push</span>Ajout de notifications Push à l'application

1.  Ouvrez le fichier projet MainPage.xaml.cs et ajoutez le code suivant chargé de créer une classe **Registrations** :

        public class Registrations
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "handle")]
            public string Handle { get; set; }
        }

    La propriété Handle est utilisée pour stocker l'URI de canal.

2.  Ouvrez le fichier App.xaml.cs et ajoutez l'instruction using suivante :

        using Windows.Networking.PushNotifications;

3.  Ajoutez le code suivant à App.xaml.cs :

        private async void AcquirePushChannel()
        {
           CurrentChannel = 
               await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

           IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
           var registration = new Registrations { Handle = CurrentChannel.Uri };
           await registrationsTable.InsertAsync(registration);
        }

    Ce code permet d'insérer le canal actif dans la table Registrations.

4.  En haut du gestionnaire d'événements **OnLaunched** dans App.xaml.cs, ajoutez l'appel suivant à la nouvelle méthode **AcquirePushChannel** :

        AcquirePushChannel();

	Cela garantit que la propriété **CurrentChannel** est initialisée à chaque lancement de l'application.

1.  Ouvrez le fichier Package.appxmanifest et vérifiez sous l'onglet **Interface utilisateur de l'application** que la valeur définie de **Compatible toast** est bien **Oui**.

    ![][]

    Cela permet de s'assurer que votre application peut déclencher des notifications toast.

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
                            push.wns.sendToastText04(registration.handle, {
                                text1: item.text
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

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][2]

    Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de WNS.

    ![][3]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel présente la fonctionnalité de notification Push de base fournie par Mobile Services. Si votre application requiert des fonctionnalités plus avancées, comme l'envoi de notifications interplateforme, le routage basé sur l'abonnement ou des volumes très importants, envisagez l'utilisation d'Azure Notification Hubs avec votre service mobile. Pour plus d'informations, consultez l'une des rubriques Notification Hubs suivantes :

-   [Prise en main de Notification Hubs][]

    En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

-   [Présentation de Notification Hubs][]

    En savoir plus sur la création et l'envoi de notifications Push aux utilisateurs sur plusieurs plateformes.

-   [Envoi de notifications aux abonnés][]

    En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

-   [Envoi de notifications aux utilisateurs][]

    En savoir plus sur l'envoi de notifications Push depuis un service mobile à certains utilisateurs sur n'importe quel appareil.

-   [Envoi de notifications interplateforme aux utilisateurs][]

    En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données][]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services .NET][]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

  [Windows Store C#]: /fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012 "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/develop/mobile/tutorials/get-started-with-push-js-vs2012 "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone"
  [iOS]: /fr-fr/develop/mobile/tutorials/get-started-with-push-ios "iOS"
  [Android]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android "Android"
  [Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android"
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet
  [Inscription de votre application pour les notifications Push et configuration de Mobile Services]: #register
  [Création de la table Registrations]: #create-table
  [Ajout de notifications Push à l'application]: #add-push
  [Mise à jour des scripts pour l'envoi de notifications Push]: #update-scripts
  [Insertion de données pour recevoir des notifications]: #test
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  []: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png
  [3]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png
  [Prise en main de Notification Hubs]: /fr-fr/manage/services/notification-hubs/getting-started-windows-dotnet/
  [Présentation de Notification Hubs]: /fr-fr/develop/net/how-to-guides/service-bus-notification-hubs/
  [Envoi de notifications aux abonnés]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet/
  [Envoi de notifications aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users/
  [Envoi de notifications interplateforme aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library/
