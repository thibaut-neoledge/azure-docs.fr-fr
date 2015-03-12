

Dans votre application principale, vous devez maintenant basculer vers l'envoi de modèles de notification plutôt que de charges utiles natives. Cela simplifie le code principal, car vous n'avez pas à envoyer plusieurs charges utiles pour les différentes plateformes.

Lorsque vous envoyez des notifications de modèle, vous devez uniquement fournir un ensemble de propriétés. Dans notre cas, nous enverrons l'ensemble des propriétés contenant la version localisée des actualités, par exemple :

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


Cette section présente deux façons d'envoyer des notifications :

- à l'aide d'une application de console ;
- à l'aide d'un script Mobile Services.

Le code inclus diffuse vers les appareils iOS et Windows Store, car le serveur principal peut diffuser vers n'importe quel appareil pris en charge.



## Envoi de notifications à l'aide d'une application console C# ##

Nous modifions simplement votre méthode *SendNotificationAsync* en envoyant un seul modèle de notification.

	var hub = NotificationHubClient.CreateClientFromConnectionString("<connection string>", "<hub name>");
    var notification = new Dictionary<string, string>() {
							{"News_English", "World News in English!"},
                            {"News_French", "World News in French!"},
                            {"News_Mandarin", "World News in Mandarin!"}};
    await hub.SendTemplateNotificationAsync(notification, "World");

Notez que ce simple appel remettra l'information localisée adéquate à **tous** vos appareils, indépendamment de leur plateforme, à mesure que votre Notification Hub crée et remet la charge utile native qui convient à l'ensemble des appareils abonnés à une balise spécifique.

### Mobile Services

Dans votre planificateur Mobile Service, remplacez votre script par :

	var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
			"News_English": "World News in English!",
			"News_French": "World News in French!",
			"News_Mandarin", "World News in Mandarin!"
	}
	notificationHubService.send('World', notification, function(error) {
		if (!error) {
			console.warn("Notification successful");
		}
	});
	
Remarquez comment, dans ce cas, il n'est pas obligatoire d'envoyer plusieurs notifications pour différents paramètres régionaux et différentes plateformes.

<!--HONumber=45--> 
