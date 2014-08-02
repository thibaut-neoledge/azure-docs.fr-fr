<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Notify Users xplat aspnet" pageTitle="Send cross-platform notifications to users with Notification Hubs (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Envoi de notifications interplateforme aux utilisateurs avec Notification Hubs
==============================================================================

[Mobile Services](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/ "Mobile Services")[ASP.NET](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet/ "ASP.NET")

Dans le didacticiel précédent [Notification des utilisateurs avec Notification Hubs](/en-us/manage/services/notification-hubs/notify-users-aspnet), vous avez appris à envoyer des notifications Push à tous les appareils inscrits par un utilisateur authentifié. Dans ce didacticiel, l'envoi d'une notification à chaque plateforme cliente prise en charge nécessitait plusieurs demandes. Notification Hubs prend en charge des modèles qui permettent de spécifier le mode de réception préféré d'un appareil déterminé. Cela simplifie l'envoi de notifications interplateforme. Cette rubrique montre comment exploiter les modèles pour envoyer, dans une même demande, une notification qui cible toutes les plateformes, quelles qu'elles soient. Pour plus d'informations sur les modèles, consultez la page [Vue d'ensemble des concentrateurs de notification](http://go.microsoft.com/fwlink/p/?LinkId=317339).

**Remarque**

Notification Hubs permet à un appareil d'inscrire plusieurs modèles avec la même balise. Dans ce cas, un message entrant ciblant cette balise déclenche l'envoi de plusieurs notifications à destination de l'appareil, une pour chaque modèle. Cela vous permet d'afficher le même message dans plusieurs notifications visuelles, par exemple, sous la forme d'un badge et d'une notification toast dans une application Windows Store.

Pour envoyer des notifications interplateforme à l'aide de modèles, procédez comme suit :

1.  Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Controllers**, puis ouvrez le fichier RegisterController.cs.

2.  Recherchez le bloc de code de la méthode **Post** qui permet de créer une inscription lorsque la valeur de `updated` est **false** et remplacez-le par le code suivant :

         if (!updated)
         {
             switch (platform)
             {
                 case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                     await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });
                     break;
                 case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";
                     await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });
                     break;
             } 
         }

    Ce code permet d'appeler la méthode propre à la plateforme pour créer une inscription de modèle et non une inscription native. Les inscriptions existantes n'ont pas besoin d'être modifiées, car les inscriptions de modèle sont dérivées d'inscriptions natives.

3.  Remplacez la méthode **sendNotification** par le code suivant :

         // Envoyez une notification interplateforme à l'aide de modèles. 
         private async Task sendNotification(string notificationText, string tag)
         {           
                 var notification = new Dictionary<string, string> { { "message", "Hello, " + tag } };
                 await hubClient.SendTemplateNotificationAsync(notification, tag);        
         }

    Ce code permet d'envoyer une notification à toutes les plateformes à la fois et sans avoir à spécifier de charge utile native. Notification Hubs génère et remet la charge utile appropriée à chaque appareil avec la valeur de *balise* fournie, comme spécifié dans les modèles inscrits.

4.  Réexécutez l'application cliente et vérifiez la réussite de l'inscription.

5.  (Facultatif) Déployez l'application cliente sur un deuxième appareil, puis exécutez l'application.

    À noter qu'une notification s'affiche sur chaque appareil.

Étapes suivantes
----------------

Maintenant que vous avez terminé ce didacticiel, vous trouverez des informations supplémentaires sur Notification Hubs et les modèles dans les rubriques suivantes :

-   **Utilisation de Notification Hubs pour envoyer les dernières nouvelles ([Windows Store C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-dotnet))**
    Présente un autre scénario d'utilisation des modèles.

-   **[Vue d'ensemble des concentrateurs de notification](http://go.microsoft.com/fwlink/p/?LinkId=317339)**
    Rubrique générale offrant des informations plus détaillées sur les modèles.

-   **[Procédures Notification Hubs pour Windows Store](http://msdn.microsoft.com/en-us/library/windowsazure/jj927172.aspx)**
    Fait notamment référence au langage d'expression de modèles.


