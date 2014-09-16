<properties linkid="develop-mobile-tutorials-get-started-with-push-js" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (JavaScript)" metaKeywords="" description="Learn how to use push notifications in your Windows Store app with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Prise en main des notifications Push dans Mobile Services avec Visual Studio 2012
=================================================================================

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a> 
</div>	

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Windows Store. Dans ce didacticiel, vous allez ajouter des notifications Push utilisant les services de notifications Windows (WNS) au projet de démarrage rapide. Une fois que vous avez terminé, votre service mobile envoie une notification Push à chaque fois qu'un enregistrement est inséré.

<div class="dev-callout"><b>Remarque</b>
<p>
Ce didacticiel permet d'ajouter des notifications Push à une application Windows Store créée dans Visual Studio 2012. Visual Studio 2013 intègre de nouvelles fonctionnalités qui facilitent la configuration des notifications Push dans votre application Windows Store en utilisant Mobile Services. Pour la version Visual Studio 2013, consultez la page <a href="/en-us/develop/mobile/tutorials/get-started-with-push-js">Prise en main des notifications Push</a>.</p>
</div>

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push et configuration de Mobile Services](#register)
2.  [Création de la table Registrations](#create-table)
3.  [Ajout de notifications Push à l'application](#add-push)
4.  [Mise à jour des scripts pour l'envoi de notifications Push](#update-scripts)
5.  [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel requiert les éléments suivants :

-   Microsoft Visual Studio 2012 Express pour Windows 8 ;
-   un compte Windows Store actif.

Ce didacticiel s'appuie sur le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-js). Avant de commencer ce didacticiel, vous devez d'abord suivre intégralement [celui-ci](/en-us/develop/mobile/tutorials/get-started-with-data-js) :

<a name="register"></a>
Inscription de votre applicationInscription de votre application pour le Windows Store
--------------------------------------------------------------------------------------

Pour pouvoir envoyer des notifications Push vers des applications Windows Store à partir de Mobile Services, vous devez soumettre votre application au Windows Store. Vous devez ensuite configurer votre service mobile pour l'intégrer à WNS.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Votre service mobile et votre application sont dès lors configurés pour fonctionner avec WNS. La prochaine étape consiste à créer une table pour stocker les inscriptions.

<a name="create-table"></a>
Création d'une table
--------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<a name="add-push"></a>
Ajout de notifications PushAjout de notifications Push à l'application
----------------------------------------------------------------------

1.  Ouvrez le fichier default.js et ajoutez le fragment de code suivant dans la surcharge de méthode **app.OnActivated**, juste après la méthode **args.setPromise** :

         // Obtenez le canal pour l'application.
         var channel;
         var channelOperation = Windows.Networking.PushNotifications
             .PushNotificationChannelManager
             .createPushNotificationChannelForApplicationAsync()
             .then(function (newChannel) {
                 channel = newChannel;
             });

    Ce code acquiert et stocke un canal de notification Push à chaque lancement de l'application.

2.  Ajoutez le code suivant après le code qui crée l'instance **MobileServiceClient** :

         // Ajoutez l'URI du nouveau canal dans la table Registrations.
         var registrationsTable = client.getTable('Registrations');
         registrationsTable.insert({ handle: channel.uri });         

    Ce code permet d'insérer le canal actif dans la table Registrations.

3.  Ouvrez le fichier Package.appxmanifest et vérifiez sous l'onglet **Interface utilisateur de l'application** que la valeur définie de **Compatible toast** est bien **Oui**.

    ![][15]

    Cela permet de s'assurer que votre application peut déclencher des notifications toast. 

<a name="update-scripts"></a>
Mise à jour du script d'insertionMise à jour du script d'insertion inscrit dans le portail de gestion
-----------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Cliquez sur **TodoItem**, puis sur **Script** et sélectionnez **Insérer**.

    ![][5]

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

<a name="test"></a>
Test de l'applicationTest des notifications Push dans l'application
-------------------------------------------------------------------

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][13]

    Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de WNS.

    ![][14]

<a name="next-steps"> </a>
Étapes suivantes
----------------

Ce didacticiel présente la fonctionnalité de notification Push de base fournie par Mobile Services. Si votre application requiert des fonctionnalités plus avancées, comme l'envoi de notifications interplateforme, le routage basé sur l'abonnement ou des volumes très importants, envisagez l'utilisation d'Azure Notification Hubs avec votre service mobile. Pour plus d'informations, consultez l'une des rubriques Notification Hubs suivantes :

-   [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>En savoir plus sur l'utilisation de Notification Hubs dans votre application Windows Store.

-   [Envoi de notifications aux abonnés](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

-   [Envoi de notifications aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>En savoir plus sur l'envoi de notifications Push depuis un service mobile à certains utilisateurs sur n'importe quel appareil.

-   [Envoi de notifications interplateforme aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-js)
    <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/) 
    <br/>En savoir plus sur l'utilisation de Mobile Services avec HTML et JavaScript


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Create the Registrations table]: #create-table
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->





[5]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-insert-script-push2.png







[13]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push2.png
[15]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-js
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012

[Azure Management Portal]: https://manage.windowsazure.com/
[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
