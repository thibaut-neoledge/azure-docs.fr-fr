

Les notifications Push sont normalement envoyées dans un service principal tel que Mobile Apps ou ASP.NET à l’aide d’une bibliothèque compatible. Vous pouvez également utiliser l’API REST directement pour envoyer des messages de notification si aucune bibliothèque n’est disponible pour votre serveur principal.

La liste ci-dessous répertorie certains autres didacticiels, que vous pouvez consulter pour envoyer des notifications :

- Azure Mobile Apps : pour découvrir un exemple de la procédure d’envoi de notifications à partir d’un serveur principal Mobile Apps intégré à Notification Hubs, consultez l’article [Ajout de notifications Push à votre application iOS](../articles/app-service-mobile/app-service-mobile-ios-get-started-push.md).  
- ASP.NET : [Utilisation de Notification Hubs pour envoyer des notifications aux utilisateurs](../articles/notification-hubs/notification-hubs-aspnet-backend-ios-notify-users.md).
- Kit de développement logiciel (SDK) Java pour Azure Notification Hub : consultez [Utilisation de Notification Hubs à partir de Java](../articles/notification-hubs/notification-hubs-java-backend-how-to.md) pour envoyer des notifications à partir de Java. Il a été testé dans Eclipse pour le développement Android.
- PHP : [Utilisation de Notification Hubs à partir de PHP](../articles/notification-hubs/notification-hubs-php-backend-how-to.md).


Dans la section suivante du didacticiel, vous allez apprendre à utiliser l’[interface de Notification Hub REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) pour envoyer le message de notification directement dans votre application. Tous les appareils inscrits reçoivent la notification envoyée par n’importe quel appareil.

<!---HONumber=AcomDC_0330_2016-->