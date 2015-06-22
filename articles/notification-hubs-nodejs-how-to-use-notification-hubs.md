<properties 
	pageTitle="Utilisation de Notification Hubs avec Node.js" 
	description="Découvrez comment utiliser Notification Hubs pour envoyer des notifications Push à partir d'une application Node.js." 
	services="notification-hubs" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="mwasson"/>

# Utilisation de Notification Hubs à partir de Node.js
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js" class="current">Node.js</a>
</div>

##Vue d'ensemble

Ce guide vous montre comment utiliser Notification Hubs depuis les applications Node.js. Les scénarios abordés comprennent l'**envoi de notifications aux applications Android, iOS, Windows Phone et Windows Store**. Pour plus d'informations sur les concentrateurs de notification, consultez la section [Étapes suivantes](#next) .

##Présentation de Notification Hubs

Azure Notification Hubs offre une infrastructure multiplateforme conviviale et extensible pour l'envoi de notifications Push aux appareils mobiles. Pour plus d'informations, consultez la rubrique [Azure Notification Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure][nodejswebsite], [Service cloud Node.js][Service cloud Node.js] (avec Windows PowerShell) ou [Site Web avec WebMatrix].

##Configuration de l'application pour utiliser Notification Hubs

Pour utiliser Azure Notification Hubs, vous devez télécharger et utiliser le package Azure Node.js. Ce dernier inclut un ensemble de bibliothèques qui
communiquent avec les services REST.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure** dans la fenêtre de commande pour générer la sortie suivante :

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  Vous pouvez exécuter manuellement la commande **ls** ou **dir** pour vérifier qu'un dossier **node_modules** a été créé. Dans ce dossier, recherchez le package **azure**, qui contient les bibliothèques nécessaires pour accéder à Notification Hubs.

### Importation du module

À l'aide d'un éditeur de texte, ajoutez la commande suivante au début du fichier **server.js** de l'application :

    var azure = require('azure');

### Configuration d'une connexion Azure Notification Hubs

L'objet **NotificationHubService** vous permet d'utiliser les concentrateurs de notification. Le code suivant crée un objet **NotificationHubService** pour le concentrateur de notification nommé **hubname**. Ajoutez-le au début du fichier **server.js**, après l'instruction relative à l'importation du module azure :

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Pour obtenir la valeur de connexion **connectionstring** à partir du portail de gestion Azure, suivez les étapes ci-dessous :

1. Sur le portail de gestion Azure, sélectionnez **Service Bus**, puis l'espace de noms qui contient le concentrateur de notification.

2. Sélectionnez **NOTIFICATION HUBS**, puis le concentrateur à utiliser.

3. Sélectionnez **Afficher la chaîne de connexion** dans la section **aperçu rapide** et copiez la valeur de la chaîne de connexion.

> [AZURE.NOTE] Vous pouvez également extraire la chaîne de connexion à l'aide de la cmdlet **Get-AzureSbNamespace** fournie par Azure PowerShell ou de la commande **azure sb namespace show** avec les outils en ligne de commande Azure.

</div>

##Envoi de notifications

L'objet **NotificationHubService** expose les instances d'objet suivantes pour l'envoi de notifications à des appareils et applications spécifiques :

* **Android** : utilisez l'objet **GcmService**, qui est disponible au niveau de **notificationHubService.gcm**
* **iOS** : utilisez l'objet **ApnsService**, qui est disponible au niveau de **notificationHubService.apns**
* **Windows Phone** : utilisez l'objet **MpnsService**,qui est disponible au niveau de **notificationHubService.mpns**
* **Applications Windows Store applications** : utilisez l'objet **WnsService**, qui est disponible au niveau de **notificationHubService.wns**

### Envoi de notifications d'application Android

L'objet **GcmService** fournit une méthode d'**envoi** que vous pouvez utiliser pour envoyer des notifications à des applications Android. Cette méthode d'**envoi** accepte les paramètres suivants :

* Tags : identifiant de balise. Si aucune balise n'est fournie, la notification est envoyée à tous les clients.
* Payload : charge utile JSON ou de chaîne du message.
* Callback : fonction de rappel.

Pour plus d'informations sur le format de charge utile, consultez la section Charge utile de l'article [Implémentation du serveur GCM](http://developer.android.com/google/gcm/server.html#payload).

Le code suivant utilise l'instance **GcmService** exposée par **NotificationHubService** pour envoyer un message à tous les clients.

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Envoi de notifications aux applications iOS

L'objet **ApnsService** fournit une méthode d'**envoi** que vous pouvez utiliser pour envoyer des notifications à des applications iOS. Cette méthode d'**envoi** accepte les paramètres suivants :

* Tags : identifiant de balise. Si aucune balise n'est fournie, la notification est envoyée à tous les clients.
* Payload : charge utile JSON ou de chaîne du message.
* Callback : fonction de rappel.

Pour plus d'informations sur le format de charge utile, consultez la section Charge utile de notification du [Guide de programmation des notifications locales et Push](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html).

Le code suivant utilise l'instance **ApnsService** exposée par **NotificationHubService** pour envoyer un message d'alerte à tous les clients :

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Envoi de notifications Windows Phone

L'objet **MpnsService** fournit une méthode d'**envoi** que vous pouvez utiliser pour envoyer des notifications à des applications Windows Phone. Cette méthode d'**envoi** accepte les paramètres suivants :

* Tags : identifiant de balise. Si aucune balise n'est fournie, la notification est envoyée à tous les clients.
* Payload : charge utile XML du message.
* TargetName : " toast " pour les notifications toast. " token " pour les notifications par vignette.
* NotificationClass : priorité de la notification. Consultez la section Éléments d'en-tête HTTP de l'article [Notifications Push d'un serveur](http://msdn.microsoft.com/library/hh221551.aspx) pour les valeurs valides.
* Options : en-têtes de requête facultatifs.
* Callback : fonction de rappel.

Pour une liste d'options valides pour TargetName, NotificationClass et les en-têtes, consultez l'article [Notifications Push d'un serveur](http://msdn.microsoft.com/library/hh221551.aspx).

Le code suivant utilise l'instance **MpnsService** exposée par **NotificationHubService** pour envoyer une alerte toast :

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Envoi de notifications aux applications Windows Store

L'objet **WnsService** fournit une méthode d'**envoi** que vous pouvez utiliser pour envoyer des notifications à des applications Windows Store.  Cette méthode d'**envoi** accepte les paramètres suivants :

* Tags : identifiant de balise. Si aucune balise n'est fournie, la notification est envoyée à tous les clients.
* Payload : charge utile XML du message.
* Type : type de notification.
* Options : en-têtes de requête facultatifs.
* Callback : fonction de rappel.

Pour obtenir la liste des types et en-têtes de requête valides, consultez l'article [Demande de service et en-têtes de réponse de notifications Push](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Le code suivant utilise l'instance **WnsService** exposée par **NotificationHubService** pour envoyer une alerte toast :

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## Étapes suivantes

Maintenant que vous avez appris les principes de base de l'utilisation de Notification Hubs, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Azure Notification Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Node] sur GitHub.

  [Kit de développement logiciel (SDK) Azure pour Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Étapes suivantes]: #nextsteps
  [Présentation des rubriques et des abonnements Service Bus]: #what-are-service-bus-topics
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [Création d'une application Node.js]: #Create_a_Nodejs_Application
  [Configuration de votre application pour l'utilisation de Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [Création d'une rubrique]: #How_to_Create_a_Topic
  [Création d'abonnements]: #How_to_Create_Subscriptions
  [Envoi de messages à une rubrique]: #How_to_Send_Messages_to_a_Topic
  [Réception des messages d'un abonnement]: #How_to_Receive_Messages_from_a_Subscription
  [Gestion des blocages d'application et des messages illisibles]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Suppression de rubriques et d'abonnements]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Concepts de rubrique]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Portail de gestion Azure]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Site web avec WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Service cloud Node.js]: cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Ancien portail de gestion]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Service cloud Node.js avec stockage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Application web Node.js avec stockage]: /develop/nodejs/tutorials/web-site-with-storage/


<!--HONumber=49-->