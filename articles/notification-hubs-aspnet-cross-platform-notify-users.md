<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Notify Users xplat aspnet" pageTitle="Send cross-platform notifications to users with Notification Hubs (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Envoi de notifications interplateforme aux utilisateurs avec Notification Hubs

Dans le didacticiel précédent [Notification des utilisateurs avec Notification Hubs][], vous avez appris à envoyer des notifications Push à tous les appareils inscrits par un utilisateur authentifié. Dans ce didacticiel, l'envoi d'une notification à chaque plateforme cliente prise en charge nécessitait plusieurs demandes. Notification Hubs prend en charge des modèles qui permettent de spécifier le mode de réception préféré d'un appareil déterminé. Cela simplifie l'envoi de notifications interplateforme. Cette rubrique montre comment exploiter les modèles pour envoyer, dans une même demande, une notification qui cible toutes les plateformes, quelles qu'elles soient. Pour plus d'informations sur les modèles, consultez la page [Vue d'ensemble des concentrateurs de notification][].

<div class="dev-callout"><b>Remarque</b>
    <p>Notification Hubs permet &agrave; un appareil d'inscrire plusieurs mod&egrave;les avec la m&ecirc;me balise. Dans ce cas, un message entrant ciblant cette balise d&eacute;clenche l'envoi de plusieurs notifications &agrave; destination de l'appareil, une pour chaque mod&egrave;le. Cela vous permet d'afficher le m&ecirc;me message dans plusieurs notifications visuelles, par exemple, sous la forme d'un badge et d'une notification toast dans une application Windows Store.</p>
</div>

Pour envoyer des notifications interplateforme à l'aide de modèles, procédez comme suit :

1.  Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Controllers**, puis ouvrez le fichier RegisterController.cs.

2.  Recherchez le bloc de code dans la méthode **Post** qui crée une inscription. Remplacez le contenu de `switch` par le code suivant :

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":\"alert\":\"$(message)\"}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":\"msg\":\"$(message)\"}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    Ce code permet d'appeler la méthode propre à la plateforme pour créer une inscription de modèle et non une inscription native. Les inscriptions existantes n'ont pas besoin d'être modifiées, car les inscriptions de modèle sont dérivées d'inscriptions natives.

3.  Dans le contrôleur **Notifications**, remplaez la méthode **sendNotification** par le code suivant :

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);   

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    Ce code permet d'envoyer une notification à toutes les plateformes à la fois et sans avoir à spécifier de charge utile native. Notification Hubs génère et remet la charge utile appropriée à chaque appareil avec la valeur de *balise* fournie, comme spécifié dans les modèles inscrits.

4.  Republiez votre projet principal WebApi.

5.  Réexécutez l'application cliente et vérifiez la réussite de l'inscription.

6.  (Facultatif) Déployez l'application cliente sur un deuxième appareil, puis exécutez l'application.

    À noter qu'une notification s'affiche sur chaque appareil.

## Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, vous trouverez des informations supplémentaires sur Notification Hubs et les modèles dans les rubriques suivantes :

-   **Utilisation de Notification Hubs pour envoyer les dernières nouvelles ([Windows Store C#][] / [iOS][Windows Store C#])**
    Présente un autre scénario d'utilisation des modèles.

-   **[Vue d'ensemble des concentrateurs de notification][]**
    Rubrique générale offrant des informations plus détaillées sur les modèles.

-   **[Procédures Notification Hubs pour Windows Store][]**
    Fait notamment référence au langage d'expression de modèles.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Notification des utilisateurs avec Notification Hubs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
  [Vue d'ensemble des concentrateurs de notification]: http://go.microsoft.com/fwlink/p/?LinkId=317339
  [Windows Store C#]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet
  [Procédures Notification Hubs pour Windows Store]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj927172.aspx
