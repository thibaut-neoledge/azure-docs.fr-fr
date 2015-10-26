<properties 
	pageTitle="Utilisation de Notification Hubs avec PHP" 
	description="Découvrez comment utiliser Azure Notification Hubs à partir d’un serveur principal PHP." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="php" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="yuaxu"/>

# Utilisation de Notification Hubs à partir de PHP
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Vous pouvez accéder à toutes les fonctionnalités de Notification Hubs à partir d'un serveur principal Java/PHP/Ruby en utilisant l'interface REST des hubs de notifications, comme décrit dans la rubrique MSDN [API REST de Notification Hubs](http://msdn.microsoft.com/library/dn223264.aspx).

Dans cette rubrique, nous vous montrons comment :

* créer un client REST pour les fonctionnalités de Notification Hubs en PHP ;
* suivre le [didacticiel de prise en main](notification-hubs-ios-get-started.md) pour la plateforme mobile de votre choix, en implémentant la partie concernant le serveur principal en PHP.

## Interface client
L'interface client principale peut fournir les mêmes méthodes que celles disponibles dans le [Kit de développement logiciel (SDK) .NET Notification Hubs](http://msdn.microsoft.com/library/jj933431.aspx), ce qui vous permet de traduire directement l'ensemble des didacticiels et des exemples actuellement disponibles sur ce site, enrichis par les contributions de la communauté Internet.

Tout le code est disponible dans l'[exemple de wrapper REST PHP].

Par exemple, pour créer un client :

	$hub = new NotificationHub("connection string", "hubname");	

Pour envoyer une notification iOS native :
	
	$notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
	$hub->sendNotification($notification);

## Implémentation
Si ce n'est déjà fait, suivez notre [didacticiel de prise en main] jusqu'à la dernière section, dans laquelle vous devrez implémenter le serveur principal. En outre, si vous le souhaitez, vous pouvez utiliser le code de l'[exemple de wrapper PHP REST] et accéder directement à la section du didacticiel [Suivi du didacticiel](#complete-tutorial).

Tous les détails de l'implémentation d'un wrapper REST complet se trouvent sur [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Dans cette section, nous allons décrire l'implémentation PHP des principales étapes requises pour accéder aux point de terminaison REST de Notification Hubs :

1. Analyse de la chaîne de connexion
2. Génération du jeton d'autorisation
3. Réalisation de l'appel HTTP

### Analyse de la chaîne de connexion

Voici la classe principale implémentant le client, dont le constructeur analyse la chaîne de connexion :

	class NotificationHub {
		const API_VERSION = "?api-version=2013-10";
	
		private $endpoint;
		private $hubPath;
		private $sasKeyName;
		private $sasKeyValue;
	
		function __construct($connectionString, $hubPath) {
			$this->hubPath = $hubPath;
	
			$this->parseConnectionString($connectionString);
		}
	
		private function parseConnectionString($connectionString) {
			$parts = explode(";", $connectionString);
			if (sizeof($parts) != 3) {
				throw new Exception("Error parsing connection string: " . $connectionString);
			}
	
			foreach ($parts as $part) {
				if (strpos($part, "Endpoint") === 0) {
					$this->endpoint = "https" . substr($part, 11);
				} else if (strpos($part, "SharedAccessKeyName") === 0) {
					$this->sasKeyName = substr($part, 20);
				} else if (strpos($part, "SharedAccessKey") === 0) {
					$this->sasKeyValue = substr($part, 16);
				}
			}
		}
	}


### Création du jeton de sécurité
Les détails concernant la création d'un jeton de sécurité sont disponibles [ici](http://msdn.microsoft.com/library/dn495627.aspx). La méthode suivante doit être ajoutée à la classe **NotificationHub** pour créer le jeton à partir de l'URI de la demande actuelle et des informations d'identification extraites de la chaîne de connexion.

	private function generateSasToken($uri) {
		$targetUri = strtolower(rawurlencode(strtolower($uri)));

		$expires = time();
		$expiresInMins = 60;
		$expires = $expires + $expiresInMins * 60;
		$toSign = $targetUri . "\n" . $expires;

		$signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

		$token = "SharedAccessSignature sr=" . $targetUri . "&sig="
					. $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

		return $token;
	}

### Envoi d’une notification
Commençons par définir une classe représentant une notification.

	class Notification {
		public $format;
		public $payload;
	
		# array with keynames for headers
		# Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
		# Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
		public $headers;
	
		function __construct($format, $payload) {
			if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
				throw new Exception('Invalid format: ' . $format);
			}
	
			$this->format = $format;
			$this->payload = $payload;
		}
	}

Cette classe est un conteneur pour un corps de notification natif ou un ensemble de propriétés dans le cas d'un modèle de notification, et un ensemble d'en-têtes contenant le format (plateforme native ou modèle) et des propriétés spécifiques de la plateforme (telles que la propriété expiration d'Apple et les en-têtes WNS).

Pour connaître toutes les options disponibles, reportez-vous à la [documentation sur les API REST de Notification Hubs](http://msdn.microsoft.com/library/dn495827.aspx) et aux formats spécifiques des plateformes de notification.

Munis de cette classe, nous pouvons à présent écrire les méthodes d'envoi des notifications à l'intérieur de la classe **NotificationHub**.

	public function sendNotification($notification) {
		$this->sendNotification($notification, "");
	}

	public function sendNotification($notification, $tagsOrTagExpression) {
		if (is_array($tagsOrTagExpression)) {
			$tagExpression = implode(" || ", $tagsOrTagExpression);
		} else {
			$tagExpression = $tagsOrTagExpression;
		}

		# build uri
		$uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
		$ch = curl_init($uri);

		if (in_array($notification->format, ["template", "apple", "gcm"])) {
			$contentType = "application/json";
		} else {
			$contentType = "application/xml";
		}

		$token = $this->generateSasToken($uri);

		$headers = [
		    'Authorization: '.$token,
		    'Content-Type: '.$contentType,
		    'ServiceBusNotification-Format: '.$notification->format
		];

		if ("" !== $tagExpression) {
			$headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
		}

		# add headers for other platforms
		if (is_array($notification->headers)) {
			$headers = array_merge($headers, $notification->headers);
		}
		
		curl_setopt_array($ch, array(
		    CURLOPT_POST => TRUE,
		    CURLOPT_RETURNTRANSFER => TRUE,
		    CURLOPT_SSL_VERIFYPEER => FALSE,
		    CURLOPT_HTTPHEADER => $headers,
		    CURLOPT_POSTFIELDS => $notification->payload
		));

		// Send the request
		$response = curl_exec($ch);

		// Check for errors
		if($response === FALSE){
		    throw new Exception(curl_error($ch));
		}

		$info = curl_getinfo($ch);

		if ($info['http_code'] <> 201) {
			throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
		}
	} 

Les méthodes ci-dessus envoient une demande POST HTTP au point de terminaison /messages de votre hub de notification, avec le corps et les en-têtes corrects pour envoyer la notification.

##<a name="complete-tutorial"></a>Suivi du didacticiel
Vous pouvez à présent suivre le didacticiel de prise en main en envoyant la notification à partir d’un serveur principal PHP.

Initialisez votre client Notification Hubs (remplacez la chaîne de connexion et le nom du hub comme indiqué dans le [didacticiel de prise en main]) : $hub = new NotificationHub("connection string", "hubname");

Ajoutez ensuite le code d’envoi en fonction de la plateforme mobile cible.

### Windows Store et Windows Phone 8.1 (non-Silverlight)

	$toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
	$notification = new Notification("windows", $toast);
	$notification->headers[] = 'X-WNS-Type: wns/toast';
	$hub->sendNotification($notification);

### iOS

	$alert = '{"aps":{"alert":"Hello from PHP!"}}';
	$notification = new Notification("apple", $alert);
	$hub->sendNotification($notification);

### Android
	$message = '{"data":{"msg":"Hello from PHP!"}}';
	$notification = new Notification("gcm", $message);
	$hub->sendNotification($notification);

### Windows Phone 8.0 et 8.1 Silverlight

	$toast = '<?xml version="1.0" encoding="utf-8"?>' .
		        '<wp:Notification xmlns:wp="WPNotification">' .
		           '<wp:Toast>' .
		                '<wp:Text1>Hello from PHP!</wp:Text1>' .
		           '</wp:Toast> ' .
		        '</wp:Notification>';
	$notification = new Notification("mpns", $toast);
	$notification->headers[] = 'X-WindowsPhone-Target : toast';
	$notification->headers[] = 'X-NotificationClass : 2';
	$hub->sendNotification($notification);


### Kindle Fire
	$message = '{"data":{"msg":"Hello from PHP!"}}';
	$notification = new Notification("adm", $message);
	$hub->sendNotification($notification);

L'exécution de votre code PHP produit normalement une notification qui apparaît sur votre appareil cible.


## Étapes suivantes
Dans cette rubrique, nous vous avons montré comment créer un client REST Java simple pour Notification Hubs. À ce stade, vous pouvez :

* télécharger l'intégralité de l'[exemple de wrapper REST PHP], qui contient tout le code ci-dessus ;
* poursuivre l'apprentissage de la fonction de balisage dans le [didacticiel Nouvelles de dernière minute] ;
* vous familiariser avec l'envoi de notifications Push à des utilisateurs individuels dans le [didacticiel Envoi de notifications à des utilisateurs]

Pour plus d’informations, consultez également le [Centre pour développeurs PHP](/develop/php/).

[exemple de wrapper PHP REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[exemple de wrapper REST PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[didacticiel de prise en main]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
 

<!---HONumber=Oct15_HO3-->