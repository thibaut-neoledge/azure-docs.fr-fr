

Les notifications Push sont normalement envoyées dans un service principal tel que Mobile Services ou ASP.NET à l’aide d’une bibliothèque compatible. Vous pouvez également utiliser l’API REST directement pour envoyer des messages de notification si aucune bibliothèque n’est disponible pour votre serveur principal.

La liste ci-dessous répertorie certains autres didacticiels, que vous pouvez consulter pour envoyer des notifications :

- Azure Mobile Services : pour obtenir un exemple expliquant comment envoyer des notifications à partir d’un serveur principal Azure Mobile Services intégré à Notification Hubs, consultez la page [Prise en main des notifications Push dans Mobile Services].  
- ASP.NET : [Utilisation de Notification Hubs pour envoyer des notifications aux utilisateurs].
- Kit de développement logiciel (SDK) Java pour Azure Notification Hub : consultez [Utilisation de Notification Hubs à partir de Java](../articles/notification-hubs/notification-hubs-java-backend-how-to.md) pour envoyer des notifications à partir de Java. Il a été testé dans Eclipse pour le développement Android.
- PHP : [Utilisation de Notification Hubs à partir de PHP](../articles/notification-hubs/notification-hubs-php-backend-how-to.md).


Dans la section suivante du didacticiel, vous allez apprendre à utiliser l’[interface REST des concentrateurs de notification](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) pour envoyer le message de notification directement dans votre application. Tous les appareils inscrits reçoivent la notification envoyée par n’importe quel appareil.

<!---HONumber=July15_HO2-->