<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Envoi de notifications interplateforme aux utilisateurs avec Notification Hubs

<div class="dev-center-tutorial-selector sublanding">
    <a href="/fr-fr/documentation/articles/notification-hubs-mobile-services-cross-platform-notify-users/" title="Mobile Services" class="current">Mobile Services</a>
    <a href="/fr-fr/documentation/articles/notification-hubs-aspnet-cross-platform-notify-users/" title="ASP.NET">ASP.NET</a>
</div>

Dans le didacticiel précédent [Notification des utilisateurs avec Notification Hubs][Notification des utilisateurs avec Notification Hubs], vous avez appris à envoyer des notifications Push à tous les appareils inscrits par un utilisateur authentifié. Dans ce didacticiel, l'envoi d'une notification à chaque plateforme cliente prise en charge nécessitait plusieurs demandes. Notification Hubs prend en charge des modèles qui permettent de spécifier le mode de réception préféré d'un appareil déterminé. Cela simplifie l'envoi de notifications interplateforme. Cette rubrique montre comment exploiter les modèles pour envoyer, dans une même demande, une notification qui cible toutes les plateformes, quelles qu'elles soient. Pour plus d'informations sur les modèles, consultez la page [Vue d'ensemble des concentrateurs de notification][Vue d'ensemble des concentrateurs de notification].

<div class="dev-callout"><b>Remarque</b>
    <p>Notification Hubs permet &agrave; un appareil d'inscrire plusieurs mod&egrave;les avec la m&ecirc;me balise. Dans ce cas, un message entrant ciblant cette balise d&eacute;clenche l'envoi de plusieurs notifications &agrave; destination de l'appareil, une pour chaque mod&egrave;le. Cela vous permet d'afficher le m&ecirc;me message dans plusieurs notifications visuelles, par exemple, sous la forme d'un badge et d'une notification toast dans une application Windows Store.</p>
</div>

Pour envoyer des notifications interplateforme à l'aide de modèles, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

    ![][0]

2.  Cliquez sur l'onglet **API**, puis sur l'entrée **register\_notifications** dans la table API.

    ![][1]

3.  Cliquez sur l'onglet **Script**, recherchez le bloc **else** qui crée une inscription quand `existingRegs` a la valeur **false**, puis remplacez-le par le code suivant :

        else {
            // Create a new registration.
            var template;
            if (platform === 'win8') {                
                template = { text1: '$(message)' };              
                hub.wns.createToastText01Registration(request.body.channelUri, 
                [userId, installationId], template, registrationComplete);
            } else if (platform === 'ios') {
                template = '{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}';
                hub.apns.createTemplateRegistration(request.body.deviceToken, 
                [userId, installationId], template, registrationComplete);
            } else {
                response.send(500, 'Unknown client.');
            }
        }

    Ce code permet d'appeler la méthode propre à la plateforme pour créer une inscription de modèle et non une inscription native. Les inscriptions existantes n'ont pas besoin d'être modifiées, car les inscriptions de modèle sont dérivées d'inscriptions natives.

4.  Cliquez sur l’onglet **Données**, puis sur la table **TodoItem**.

    ![][2]

5.  Dans **todoitem**, cliquez sur l'onglet **Script**, sélectionnez **Insert**, puis remplacez la fonction **insert** existante par le code suivant :

    ![][3]

    La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

6.  Remplacez la fonction d'insertion par le code suivant :

        function insert(item, user, request) {
            var azure = require('azure');
            var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
            '<FULL_SAS_CONNECTION_STRING>');

            // Execute the request and send notifications.
            request.execute({
                success: function() {
                    // Write the default response and send a notification 
                    // to the user on all devices by using the userId tag.
                    request.respond();
                    // Create a template-based payload.
                    var payload = '{ "message" : "New item added: ' + item.text + '" }';            
                    // Send a notification to the current user on all platforms. 
                    hub.send(user.userId, payload,  
                    function(error, outcome){
                        // Do something here with the outcome or error.
                    });     
                }
            });
        }

    Ce code permet d'envoyer une notification à toutes les plateformes à la fois et sans avoir à spécifier de charge utile native. Notification Hubs génère et remet la charge utile appropriée à chaque appareil avec la valeur de *balise* fournie, comme spécifié dans les modèles inscrits.

7.  Mettez à jour le script pour remplacer *`<NOTIFICATION_HUB_NAME>`* et *`<FULL_SAS_CONNECTION_STRING>`* par les valeurs de votre concentrateur de notification, puis cliquez sur **Enregistrer**.

8.  Arrêtez l'émulateur d'appareil ou désinstallez l'application cliente existante de l'appareil.

    Cela apporte la garantie qu'un nouvel ID d'installation est généré par le client Mobile Services. Si vous ne faites pas cela, le service tente d'utiliser l'inscription existante non liée à un modèle.

9.  Déployez et réexécutez le client, puis vérifiez que l'inscription aboutit et qu'un nouvel ID d'inscription s'affiche.

10. Entrez le texte requis comme précédemment et ajoutez le nouvel élément.

    Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de Notification Hubs.

11. (Facultatif) Déployez l'application cliente sur un second appareil, puis exécutez-la et insérez du texte.

    À noter qu'une notification s'affiche sur chaque appareil.

## Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, vous trouverez des informations supplémentaires sur Notification Hubs et les modèles dans les rubriques suivantes :

-   **Utilisation de Notification Hubs pour envoyer les dernières nouvelles ([Windows Store C#][Windows Store C#] / [iOS][iOS])**
    Présente un autre scénario d'utilisation des modèles.

-   **[Vue d'ensemble des concentrateurs de notification][Vue d'ensemble des concentrateurs de notification]**
    Rubrique générale offrant des informations plus détaillées sur les modèles.

-   **[Procédures Notification Hubs pour Windows Store][Procédures Notification Hubs pour Windows Store]**
    Fait notamment référence au langage d'expression de modèles.

 
 


  [Notification des utilisateurs avec Notification Hubs]: /fr-fr/manage/services/notification-hubs/notify-users
  [Vue d'ensemble des concentrateurs de notification]: http://go.microsoft.com/fwlink/p/?LinkId=317339
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
  [1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
  [2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
  [3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
  [Windows Store C#]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet
  [iOS]: /fr-fr/manage/services/notification-hubs/breaking-news-ios
  [Procédures Notification Hubs pour Windows Store]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj927172.aspx
