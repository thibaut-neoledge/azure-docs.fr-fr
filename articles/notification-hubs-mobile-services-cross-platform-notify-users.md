<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Envoi de notifications interplateforme aux utilisateurs avec Notification Hubs
==============================================================================

[Mobile Services](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/ "Mobile Services") [ASP.NET](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet/ "ASP.NET")

Dans le didacticiel précédent [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users), vous avez appris à envoyer des notifications Push à tous les appareils inscrits par un utilisateur authentifié. Dans ce didacticiel, l'envoi d'une notification à chaque plateforme cliente prise en charge nécessitait plusieurs demandes. Notification Hubs prend en charge des modèles qui permettent de spécifier le mode de réception préféré d'un appareil déterminé. Cela simplifie l'envoi de notifications interplateforme. Cette rubrique montre comment exploiter les modèles pour envoyer, dans une même demande, une notification qui cible toutes les plateformes, quelles qu'elles soient. Pour plus d'informations sur les modèles, consultez la page [Vue d'ensemble des concentrateurs de notification](http://go.microsoft.com/fwlink/p/?LinkId=317339).
**Remarque**

Notification Hubs permet à un appareil d'inscrire plusieurs modèles avec la même balise. Dans ce cas, un message entrant ciblant cette balise déclenche l'envoi de plusieurs notifications à destination de l'appareil, une pour chaque modèle. Cela vous permet d'afficher le même message dans plusieurs notifications visuelles, par exemple, sous la forme d'un badge et d'une notification toast dans une application Windows Store.

Pour envoyer des notifications interplateforme à l'aide de modèles, procédez comme suit :

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

      ![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png)

2.  Cliquez sur l'onglet **API**, puis sur l'entrée **register\_notifications** dans la table API.

    ![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png)

3.  Cliquez sur l'onglet **Script**, recherchez le bloc **else** qui crée un enregistrement lorsque `existingRegs` a la valeur **false**, puis remplacez-le par le code suivant :

         else {
             // Créez une nouvelle inscription.
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

      ![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png)

5.  Dans **todoitem**, cliquez sur l'onglet **Script**, sélectionnez **Insert**, puis remplacez la fonction **insert** existante par le code suivant :

  ![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png) 

  La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

1.  Remplacez la fonction d'insertion par le code suivant :

         function insert(item, user, request) {
             var azure = require('azure');
             var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
             '<FULL_SAS_CONNECTION_STRING>');
            
             // Exécutez la requête et envoyez les notifications.
             request.execute({
                 success: function() {
                     // Écrivez la réponse par défaut et envoyez une notification 
                     // à l'utilisateur sur tous les appareils en utilisant la balise userId.
                     request.respond();
                     // Créez une charge utile basée sur un modèle.
                     var payload = '{ "message" : "New item added: ' + item.text + '" }';            
                     // Envoyez une notification à l'utilisateur actif sur toutes les plateformes. 
                     hub.send(user.userId, payload,  
                     function(error, outcome){
                         // Agissez ici en fonction du résultat ou de l'erreur.
                     });     
                 }
             });
         }

    Ce code permet d'envoyer une notification à toutes les plateformes à la fois et sans avoir à spécifier de charge utile native. Notification Hubs génère et remet la charge utile appropriée à chaque appareil avec la valeur de *balise* fournie, comme spécifié dans les modèles inscrits.

2.  Mettez à jour le script en remplaçant *`<NOTIFICATION_HUB_NAME>`* et *`<FULL_SAS_CONNECTION_STRING>`* par les valeurs de votre Notification Hub, puis cliquez sur **Enregistrer**.

3.  Arrêtez l'émulateur d'appareil ou désinstallez l'application cliente existante de l'appareil.

    Cela apporte la garantie qu'un nouvel ID d'installation est généré par le client Mobile Services. Si vous ne faites pas cela, le service tente d'utiliser l'inscription existante non liée à un modèle.

4.  Déployez et réexécutez le client, puis vérifiez que l'inscription aboutit et qu'un nouvel ID d'inscription s'affiche.

5.  Entrez le texte requis comme précédemment et ajoutez le nouvel élément.

    Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de Notification Hubs.

6.  (Facultatif) Déployez l'application cliente sur un deuxième appareil, puis exécutez-la et insérez du texte.

    À noter qu'une notification s'affiche sur chaque appareil.

Étapes suivantes
----------------

Maintenant que vous avez terminé ce didacticiel, vous trouverez des informations supplémentaires sur Notification Hubs et les modèles dans les rubriques suivantes :

-   **Utilisation de Notification Hubs pour envoyer les dernières nouvelles ([Windows Store C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-ios))**
    Présente un autre scénario d'utilisation des modèles.

-   **[Vue d'ensemble des concentrateurs de notification](http://go.microsoft.com/fwlink/p/?LinkId=317339)**
    Rubrique générale offrant des informations plus détaillées sur les modèles.

-   **[Procédures Notification Hubs pour Windows Store](http://msdn.microsoft.com/en-us/library/windowsazure/jj927172.aspx)**
    Fait notamment référence au langage d'expression de modèles.


