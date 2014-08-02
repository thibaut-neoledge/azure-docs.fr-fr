<properties linkid="develop-mobile-tutorials-get-started-with-push-dotnet" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="push notifications c#" description="Learn how to use push notifications with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services using Visual Studio 2012" authors="" />

Prise en main des notifications Push dans Mobile Services avec Visual Studio 2012
=================================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012 "Windows Store C#") [Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012 "Windows Store JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS") [Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android") [Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application Windows Store. Dans ce didacticiel, vous allez ajouter des notifications Push utilisant les services de notifications Windows (WNS) au projet de démarrage rapide. Une fois que vous avez terminé, votre service mobile envoie une notification Push à chaque fois qu'un enregistrement est inséré.

**Remarque**

Ce didacticiel permet d'ajouter des notifications Push à une application Windows Store créée dans Visual Studio 2012. Visual Studio 2013 intègre de nouvelles fonctionnalités qui facilitent la configuration des notifications Push dans votre application Windows Store en utilisant Mobile Services. Pour la version Visual Studio 2013, consultez la page [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push et configuration de Mobile Services](#register)
2.  [Création de la table Registrations](#create-table)
3.  [Ajout de notifications Push à l'application](#add-push)
4.  [Mise à jour des scripts pour l'envoi de notifications Push](#update-scripts)
5.  [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel requiert les éléments suivants :

-   Microsoft Visual Studio 2012 Express pour Windows 8 ;
-   un compte Windows Store actif.

Ce didacticiel s'appuie sur le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet). Avant de commencer ce didacticiel, vous devez d'abord suivre intégralement [celui-ci](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet) :

Inscription de votre applicationInscription de votre application pour le Windows Store
--------------------------------------------------------------------------------------

Pour pouvoir envoyer des notifications Push vers des applications Windows Store à partir de Mobile Services, vous devez soumettre votre application au Windows Store. Vous devez ensuite configurer votre service mobile pour l'intégrer à WNS.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Votre service mobile et votre application sont dès lors configurés pour fonctionner avec WNS. La prochaine étape consiste à créer une table pour stocker les inscriptions.

Création d'une table
--------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

Ajout de notifications PushAjout de notifications Push à l'application
----------------------------------------------------------------------

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

5.  Ouvrez le fichier Package.appxmanifest et vérifiez sous l'onglet **Interface utilisateur de l'application** que la valeur définie de **Compatible toast** est bien **Oui**.

      ![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png)

      Cela permet de s'assurer que votre application peut déclencher des notifications toast. 

Mise à jour du script d'insertionMise à jour des scripts d'insertion inscrits dans le portail de gestion
--------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**.

      ![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-insert-script-push2.png)

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

Test de l'applicationTest des notifications Push dans l'application
-------------------------------------------------------------------

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

      ![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push1.png)

      Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de WNS.

      ![](./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-quickstart-push2.png)

Étapes suivantes
----------------

Ce didacticiel présente la fonctionnalité de notification Push de base fournie par Mobile Services. Si votre application requiert des fonctionnalités plus avancées, comme l'envoi de notifications interplateforme, le routage basé sur l'abonnement ou des volumes très importants, envisagez l'utilisation d'Azure Notification Hubs avec votre service mobile. Pour plus d'informations, consultez l'une des rubriques Notification Hubs suivantes :

-   [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

-   [Présentation de Notification Hubs](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)
    <br/>En savoir plus sur la création et l'envoi de notifications Push aux utilisateurs sur plusieurs plateformes.

-   [Envoi de notifications aux abonnés](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

-   [Envoi de notifications aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>En savoir plus sur l'envoi de notifications Push depuis un service mobile à certains utilisateurs sur n'importe quel appareil.

-   [Envoi de notifications interplateforme aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet)
    <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/)
    <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.


