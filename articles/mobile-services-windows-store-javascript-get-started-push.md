<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main des notifications Push dans Mobile Services
=========================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-push "Android")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-push "Xamarin.Android")
[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/ "JavaScript backend")

Cette rubrique vous explique comment, via Visual Studio 2013, utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Windows Store. Le didacticiel vous apprend à ajouter des notifications Push au projet de démarrage rapide à l'aide du Service de notifications Windows Push (WNS), directement à partir de Visual Studio. Une fois que vous avez terminé, votre service mobile envoie une notification Push à chaque fois qu'un enregistrement est inséré.

> [WACOM.NOTE]Mobile Services s'intègre désormais à Azure Notification Hubs pour prendre en charge une fonctionnalité de notification Push supplémentaire, comme les modèles, les plateformes multiples et la mise à l'échelle. Actuellement, cette fonctionnalité intégrée est uniquement disponible en tant que version préliminaire. Pour plus d'informations, consultez cette version de [Prise en main des notifications Push](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push et configuration de Mobile Services](#register)
2.  [Mise à jour du code de notification Push](#update-scripts)
3.  [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started/) ou [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-js/) pour connecter votre projet au service mobile. Lorsqu'un service mobile n'est pas connecté, l’Assistant Ajouter une notification Push crée cette connexion pour vous.

Inscription de votre application Ajout et configuration des notifications Push dans l'application
-------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Ouvrez le fichier de code push.register.js généré, puis contrôlez le code qui récupère l'ID d'installation et le canal de l'appareil, et insère ces données dans la nouvelle table **channels**.

    Cette table a été créée dans votre service mobile par l'Assistant Ajouter une notification d'émission. Ce code garantit qu'une tentative d'inscription de l'appareil est effectuée chaque fois que l'application est lancée.

2.  Dans l'Explorateur de serveurs, développez **Azure**, **Mobile Services**, votre nom de service et **channels**, puis ouvrez le fichier insert.js.

    Ce fichier, qui est stocké dans votre service mobile, contient du code JavaScript qui est exécuté lorsqu'un client envoie une demande d'inscription d'un appareil en insérant les données dans la table de canaux.

    **Remarque**

    La version initiale de ce fichier contient du code qui vérifie si l'appareil en question est déjà inscrit. Elle inclut une autre portion du code qui envoie une notification Push lorsqu'une nouvelle inscription est ajoutée à la table de canaux. Le code qui envoie une notification Push peut être inclus dans tout fichier script inscrit. L'emplacement de ce script dépend du mode de déclenchement de la notification. Les scripts peuvent être enregistrés pour une opération sur une table de type insertion, mise à jour, suppression ou lecture (tâche planifiée ou API personnalisée). Pour plus d'informations, consultez la page [Utilisation des scripts serveur dans Mobile Services](http://go.microsoft.com/fwlink/p/?LinkID=287178).

3.  Appuyez sur la touche F5 pour exécuter l'application et vérifiez que vous recevez immédiatement une notification du service mobile.

    Cette notification, générée par l'insertion d'une ligne dans la nouvelle table de canaux, correspond à l'inscription de l'appareil.

Le code généré permet de démontrer facilement l'émission d'une notification lorsque l'application est exécutée. En pratique, ce scénario n'est généralement pas significatif. Vous supprimez ensuite le code de notification de la table de canaux et le remplacez, sous une forme modifiée, dans la table TodoItem.

Mise à jour du code Mise à jour du code de notification Push généré
-------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

Test de l'applicationTest des notifications Push dans l'application
-------------------------------------------------------------------

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

      ![](./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png)

      Notez qu'une fois l'insertion terminée, l'application reçoit une notification Push de WNS.

      ![](./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png)

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

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-js/)
    <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/) 
    <br/>En savoir plus sur l'utilisation de Mobile Services avec HTML et JavaScript


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-js/
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/