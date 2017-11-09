---
title: Envoyer des notifications interplateformes aux utilisateurs avec Azure Notification Hubs (ASP.NET)
description: "Découvrez comment utiliser des modèles Notification Hubs pour envoyer, dans une même demande, une notification indépendante de la plateforme qui cible toutes les plateformes."
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 3c6dde338cb154f0cbe02642e4ff0f81d070aa25
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Envoi de notifications interplateforme aux utilisateurs avec Notification Hubs
Dans le didacticiel précédent, [Notification des utilisateurs via Notification Hubs], vous avez appris à envoyer des notifications Push à tous les appareils inscrits pour un utilisateur authentifié spécifique. Dans ce didacticiel, l'envoi d'une notification à chaque plateforme cliente prise en charge nécessitait plusieurs demandes. Azure Notification Hubs prend en charge des modèles avec lesquels vous pouvez spécifier le mode de réception des notifications pour un appareil déterminé. Cette méthode simplifie l’envoi de notifications interplateformes. 

Cet article montre comment exploiter les modèles pour envoyer, dans une même demande, une notification qui cible toutes les plateformes, quelles qu’elles soient. Pour plus d’informations sur les modèles, consultez [Vue d’ensemble d’Azure Notification Hubs][Templates].

> [!IMPORTANT]
> Les projets Windows Phone version 8.1 et antérieures ne sont pas pris en charge dans Visual Studio 2017. Pour en savoir plus, consultez [Plateforme cible et compatibilité dans Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Avec Notification Hubs, un appareil peut inscrire plusieurs modèles avec une même balise. Dans ce cas, un message entrant qui cible cette balise déclenche l’envoi de plusieurs notifications à destination de l’appareil, une pour chaque modèle. Ce processus vous permet d’afficher un même message dans plusieurs notifications visuelles, par exemple, sous la forme d’un badge et d’une notification toast dans une application du Windows Store.
> 
> 

Pour envoyer des notifications interplateformes en utilisant des modèles, procédez comme suit :

1. Dans l’Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs**, puis ouvrez le fichier RegisterController.cs.

2. Recherchez le bloc de code dans la méthode **Put** qui crée une inscription, puis remplacez le contenu de `switch` par le code suivant :
   
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
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    Ce code permet d’appeler la méthode propre à la plateforme pour créer une inscription de modèle et non une inscription native. Sachant que les inscriptions de modèles sont dérivées d’inscriptions natives, vous n’avez pas besoin de modifier les inscriptions existantes.

3. Dans le contrôleur **Notifications**, remplacez la méthode **sendNotification** par le code suivant :
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Ce code envoie une notification à toutes les plateformes à la fois, sans avoir à spécifier de charge utile native. Notification Hubs génère et remet la charge utile appropriée à chaque appareil avec la valeur de *balise* fournie, comme spécifié dans les modèles inscrits.

4. Republiez votre projet principal WebApi.

5. Réexécutez l’application cliente et vérifiez que l’inscription a abouti.

6. (Facultatif) Déployez l’application cliente sur un deuxième appareil, puis exécutez l’application.
    À noter qu'une notification s'affiche sur chaque appareil.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel, vous trouverez des informations supplémentaires sur Notification Hubs et les modèles dans les rubriques suivantes :

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Vue d’ensemble d’Azure Notification Hubs][Templates] : contient de plus amples informations sur les modèles.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notification des utilisateurs via Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
