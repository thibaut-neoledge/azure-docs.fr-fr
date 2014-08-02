<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="Notify Users" pageTitle="Notify Users of your mobile service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your mobile service by using Notification Hubs" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Notification des utilisateurs avec Notification Hubs
====================================================

[Mobile Services](/en-us/manage/services/notification-hubs/notify-users "Mobile Services") [ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

Ce didacticiel explique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à un utilisateur particulier d'une application sur un appareil spécifique. Un serveur principal Azure Mobile Services est utilisé pour authentifier les clients et générer des notifications. Ce didacticiel fait intervenir le Notification Hub que vous avez créé dans le didacticiel précédent intitulé **Prise en main de Notification Hubs**. Le code d'inscription aux notifications est transféré du client vers le service principal. Ainsi, l'inscription n'est pas terminée tant qu'un client n'a pas été authentifié correctement par le service. Cela signifie également que les informations d'identification du Notification Hub ne sont pas distribuées avec l'application cliente. Le service contrôle également les balises demandées pendant l'inscription.

Ce didacticiel vous familiarise avec les étapes de base suivantes :

-   [Mise à jour de votre service mobile pour vous inscrire afin de recevoir des notifications](#register-notification)
-   [Mise à jour de l'application pour se connecter et demander l'inscription](#update-app)
-   [Mise à jour de votre service mobile pour l'envoi de notifications](#send-notifications)

Configuration requise
---------------------

Avant de commencer ce didacticiel, vous devez suivre les didacticiels suivants :

-   **Prise en main de Notification Hubs** ([Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/en-us/manage/services/notification-hubs/get-started-notification-hubs-android)).

-   **Prise en main de l'authentification dans Mobile Services** ([Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android/))

Ce didacticiel fait intervenir l'application et le Notification Hub que vous avez créés dans le didacticiel précédent intitulé **Prise en main de Notification Hubs**. Il exploite également le service mobile authentifié que vous avez configuré lorsque vous avez suivi **Prise en main de l'authentification dans Mobile Services**.

**Remarque**

Par défaut, le didacticiel **Prise en main de l'authentification dans Mobile Services** utilise l'authentification Facebook. Il est impossible d'utiliser l'authentification de compte Microsoft, car deux applications Windows Store ne peuvent pas partager une inscription Live Connect unique. Pour y recourir, le service mobile et le Notification Hub doivent être inscrits à la même application dans Live Connect.

Inscription aux notifications Mise à jour de votre service mobile pour vous inscrire afin de recevoir des notifications
-----------------------------------------------------------------------------------------------------------------------

L'inscription aux notifications ne devant être finalisée qu'une fois le client correctement authentifié par le service, le processus est effectué via une API personnalisée définie dans le service mobile. Celle-ci est appelée par un client authentifié pour demander l'inscription aux notifications. Dans cette section, vous allez mettre à jour le service mobile authentifié que vous avez défini dans le cadre du didacticiel **Prise en main de l'authentification dans Mobile Services**.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Service Bus**, sur votre espace de noms, **Notification Hubs**, puis choisissez votre Notification Hub et cliquez sur **Informations de connexion**.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png)

2.  Notez le nom de votre Notification Hub et copiez la chaîne de connexion pour **DefaultFullSharedAccessSignature**.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png)

    Vous allez utiliser cette chaîne de connexion, avec le nom du Notification Hub, à la fois pour vous inscrire et pour envoyer des notifications.

3.  Toujours dans le portail de gestion, cliquez sur **Mobile Services**, puis sur votre application.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png)

4.  Cliquez sur l’onglet **API**, puis sur **Create a custom API**.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png)

    La boîte de dialogue **Create a new custom API** s’affiche.

5.  Tapez *register\_notifications* sous **API name**, sélectionnez **Only Authenticated Users** pour **POST Permissions**, puis cliquez sur le bouton de vérification.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png)

	Cette action crée l'API qui requiert l'authentification des utilisateurs avant un appel avec la méthode HTTP POST. Nous n'avons pas besoin de définir les autres méthodes HTTP, car elles ne seront pas mises en œuvre.

1.  Cliquez sur la nouvelle entrée **register\_notifications** dans la table API.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png)

2.  Cliquez sur l'onglet **Script** et remplacez le code existant par les lignes suivantes :

         exports.post = function(request, response) {

             // Créez une instance de Notification Hub.
             var azure = require('azure');
             var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
                 '<FULL_SAS_CONNECTION_STRING>');
            
             // Obtenez les informations d'inscription dont nous avons besoin à partir de la demande. 
             var platform = request.body.platform;
             var userId = request.user.userId;
             var installationId = request.header('X-ZUMO-INSTALLATION-ID');
                
             // Fonction appelée lorsque l'inscription est terminée.
             var registrationComplete = function(error, registration) {
                 if (!error) {
                     // Renvoyez l'inscription.
                     response.send(200, registration);
                 } else {
                     response.send(500, 'Registration failed!');
                 }
             }
             // Fonction appelée pour consigner les erreurs.
             var logErrors = function(error) {
                 if (error) {
                     console.error(error)
                 }
             }
             // Récupérez les inscriptions existantes.
             hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
                 var firstRegistration = true;
                 if (existingRegs.length > 0) {
                      for (var i = 0; i < existingRegs.length; i++) {
                         if (firstRegistration) {
                             // Mettez à jour une inscription existante.
                             if (platform === 'win8') {
                                 existingRegs[i].ChannelUri = request.body.channelUri;                        
                                 hub.updateRegistration(existingRegs[i], registrationComplete);                        
                             } else if (platform === 'ios') {
                                 existingRegs[i].DeviceToken = request.body.deviceToken;
                                 hub.updateRegistration(existingRegs[i], registrationComplete);
                             } else {
                                 response.send(500, 'Unknown client.');
                             }
                             firstRegistration = false;
                         } else {
                             // Nous ne devons pas avoir d'inscriptions supplémentaires ; les supprimer le cas échéant.
                             hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
                         }
                     }
                 } else {
                     // Créez une nouvelle inscription.
                     if (platform === 'win8') {                
                         hub.wns.createNativeRegistration(request.body.channelUri, 
                         [userId, installationId], registrationComplete);
                     } else if (platform === 'ios') {
                         hub.apns.createNativeRegistration(request.body.deviceToken, 
                         [userId, installationId], registrationComplete);
                     } else {
                         response.send(500, 'Unknown client.');
                     }
                 }
             });
         }

    Ce code récupère les informations sur la plateforme et l'ID de l'appareil dans le corps du message. Ces données, avec l'ID d'installation de l'en-tête de la demande et l'ID utilisateur de l'utilisateur connecté, sont utilisées pour mettre à jour une inscription, si elle existe, ou en créer une. Cette inscription est marquée avec l'ID utilisateur et l'ID d'installation.

3.  Mettez à jour le script en remplaçant *`<NOTIFICATION_HUB_NAME>`* et *`<FULL_SAS_CONNECTION_STRING>`* par les valeurs de votre Notification Hub, puis cliquez sur **Enregistrer**.

    **Remarque**

    Veillez à utiliser **DefaultFullSharedAccessSignature** pour *`<FULL_SAS_CONNECTION_STRING>`*. Cette revendication permet à votre méthode API personnalisée de créer et de mettre à jour des inscriptions.

Mise à jour de l'applicationMise à jour de l'application pour se connecter et demander l'inscription
----------------------------------------------------------------------------------------------------

Vous devez ensuite mettre à jour l'application TodoList pour demander une inscription aux notifications en appelant la nouvelle API personnalisée.

1.  Suivez les étapes permettant d'**inscrire l'utilisateur actuel afin qu'il reçoive des notifications Push via un service mobile**, selon la plateforme client que vous utilisez :

    -   [Version Windows Store C\#](/en-us/manage/services/notification-hubs/register-users-mobile-services-dotnet)
    -   [Version iOS](/en-us/manage/services/notification-hubs/register-users-ios)

2.  Exécutez l'application mise à jour, connectez-vous à Facebook, puis vérifiez que l'ID d'inscription attribué à la notification est affiché.

Envoi de notificationsMise à jour de votre service mobile pour l'envoi de notifications
---------------------------------------------------------------------------------------

L'étape finale consiste à ajouter la portion de code qui envoie les notifications dans le service mobile. Ce code de notification est ajouté au script serveur inscrit auprès du gestionnaire d'insertion de la table **TodoItem**.

1.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png)

2.  Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

	![](./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png) 

	La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

1.  Remplacez la fonction d'insertion par le code suivant :

         function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
 		   // Créez la charge utile pour une application Windows Store.
		    var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Exécutez la requête et envoyez les notifications.
            request.execute({
                success: function() {
                    // Écrivez la réponse par défaut et envoyez une notification 
                    // à l'utilisateur sur tous les appareils en utilisant la balise userId.
                    request.respond();
                    // Envoyez aux applications Windows Store.
                    hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
                        if (error) {
                            console.log(error);
                        }
                    });
                    // Envoyez aux applications iOS.
                    hub.apns.send(user.userId, {
                        alert: item.text
                    }, function(error) {
                        if (error) {
                            console.log(error);
                        }
                    });
                }
            });
        }

    Cette action permet de tenter d'envoyer des notifications à la balise de l'utilisateur actuellement connecté dans les applications Windows Store et iOS.

1.  Mettez à jour le script en remplaçant *`<NOTIFICATION_HUB_NAME>`* et *`<FULL_SAS_CONNECTION_STRING>`* par les valeurs de votre Notification Hub, puis cliquez sur **Enregistrer**.

    Cette mise à jour inscrit un nouveau script d'insertion, qui utilise Notification Hubs pour envoyer une notification Push (le texte inséré) au canal fourni dans la demande d'insertion.

    **Remarque**

    Veillez à utiliser **DefaultFullSharedAccessSignature** pour *`<FULL_SAS_CONNECTION_STRING>`*. Cette revendication fournit un accès complet à votre script d'insertion, avec notamment la capacité d'envoi de notifications aux clients inscrits.

Test de l'applicationTest des notifications Push dans l'application
-------------------------------------------------------------------

Maintenant que les notifications sont configurées, il est temps de tester l'application en insérant des données permettant de générer une notification.

1.  Exécutez l'application.

    Une notification d'inscription s'affiche à nouveau au démarrage.

2.  Entrez le texte requis comme précédemment et ajoutez le nouvel élément.

    Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de Notification Hubs.

    **Remarque**

    Une erreur est générée sur le serveur principal lorsqu'il n'existe pas d'inscription pour une plateforme vers laquelle l'envoi d'une notification a été demandé. Dans ce cas, cette erreur peut être ignorée. Pour voir comment éviter cette situation en utilisant des modèles, consultez la page [Envoi de notifications interplateforme aux utilisateurs avec les Notification Hubs](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/).

3.  (Facultatif) Déployez l'application cliente sur un second appareil, puis exécutez-la et insérez du texte.

    Une notification s'affiche sur chaque appareil.

Étapes suivantes
----------------

Maintenant que vous êtes parvenu à la fin de ce didacticiel, vous pouvez poursuivre avec les didacticiels suivants :

-   **Utilisation des Notification Hubs pour diffuser les dernières nouvelles ([Windows Store C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-ios))**
    Ce didacticiel propre à la plateforme vous montre comment utiliser des balises pour permettre aux utilisateurs de s'abonner aux types de notifications qui les intéressent.

-   **[Envoi de notifications interplateforme aux utilisateurs avec les Notification Hubs](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services)**
    Ce didacticiel prolonge le présent didacticiel **Notification des utilisateurs avec les Notification Hubs** et explique comment s'inscrire aux notifications en utilisant des modèles propres à la plateforme. Cela vous permet d'envoyer des notifications à partir d'une seule méthode dans votre code côté serveur.

Pour plus d'informations sur Notification Hubs, consultez la page [Azure Notification Hubs](http://msdn.microsoft.com/en-us/library/windowsazure/jj927170.aspx).

