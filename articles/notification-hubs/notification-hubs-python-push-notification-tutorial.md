<properties 
	pageTitle="Utilisation de Notification Hubs avec Python" 
	description="Découvrez comment utiliser Azure Notification Hubs à partir d'un serveur principal Python." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777"
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="python" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="wesmc"/>

# Utilisation de Notification Hubs à partir de Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
		
Vous pouvez accéder à toutes les fonctionnalités Notification Hubs à partir d'un serveur principal Java/PHP/Python/Ruby en utilisant l'interface REST Notification Hub, comme décrit dans la rubrique MSDN [API REST Notification Hubs](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE] Ceci est un exemple d’implémentation de référence pour l’implémentation des envois de notifications dans Python. Il ne s’agit pas du Kit de développement logiciel (SDK) de Notification Hub Python officiellement pris en charge.

> [AZURE.NOTE] Cet exemple a été écrit à l’aide de Python 3.4.

Dans cette rubrique, nous vous montrons comment :

* créer un client REST pour les fonctionnalités de Notification Hubs dans Python ;
* envoyer des notifications à l’aide de l’interface de Python vers les API REST Notification Hub ;
* obtenir un vidage de la demande/réponse HTTP REST à des fins pédagogiques/de débogage.

Vous pouvez suivre le [didacticiel de prise en main](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) pour la plateforme mobile de votre choix, en implémentant la partie concernant le serveur principal dans Python.

> [AZURE.NOTE] L’étendue de l’exemple est limitée uniquement à l’envoi de notifications et il n’effectue aucune gestion des inscriptions.

## Interface client
L'interface client principale peut fournir les mêmes méthodes que celles disponibles dans le [Kit de développement logiciel (SDK) .NET Notification Hubs](http://msdn.microsoft.com/library/jj933431.aspx). Cela vous permet de traduire directement les didacticiels et les exemples actuellement disponibles sur ce site et fournis par la communauté sur Internet.

Tout le code est disponible dans l'[exemple de wrapper REST Python].

Par exemple, pour créer un client :

	isDebug = True
	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
	
Pour envoyer une notification toast de Windows :
	
	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Hello world!</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)
	
## Implémentation
Si ce n'est déjà fait, suivez notre [didacticiel de prise en main] jusqu'à la dernière section, dans laquelle vous devrez implémenter le serveur principal.

Tous les détails de l'implémentation d'un wrapper REST complet se trouvent sur [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). Dans cette section, nous allons décrire l’implémentation Python des principales étapes requises pour accéder aux point de terminaison REST de Notification Hubs et envoyer des notifications :

1. Analyse de la chaîne de connexion
2. Génération du jeton d’autorisation
3. Envoyer une notification à l’aide de l’API REST de HTTP

### Analyse de la chaîne de connexion

Voici la classe principale implémentant le client, dont le constructeur analyse la chaîne de connexion :

	class NotificationHub:
	    API_VERSION = "?api-version=2013-10"
	    DEBUG_SEND = "&test"
	
	    def __init__(self, connection_string=None, hub_name=None, debug=0):
	        self.HubName = hub_name
	        self.Debug = debug
	
	        # Parse connection string
	        parts = connection_string.split(';')
	        if len(parts) != 3:
	            raise Exception("Invalid ConnectionString.")
	
	        for part in parts:
	            if part.startswith('Endpoint'):
	                self.Endpoint = 'https' + part[11:]
	            if part.startswith('SharedAccessKeyName'):
	                self.SasKeyName = part[20:]
	            if part.startswith('SharedAccessKey'):
	                self.SasKeyValue = part[16:]


### Création du jeton de sécurité
Les détails concernant la création d'un jeton de sécurité sont disponibles [ici](http://msdn.microsoft.com/library/dn495627.aspx). Les méthodes suivantes doivent être ajoutées à la classe **NotificationHub** pour créer le jeton à partir de l'URI de la demande actuelle et des informations d'identification extraites de la chaîne de connexion.

	@staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### Envoyer une notification à l’aide de l’API REST de HTTP
Commençons par définir une classe représentant une notification.

	class Notification:
	    def __init__(self, notification_format=None, payload=None, debug=0):
	        valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
	        if not any(x in notification_format for x in valid_formats):
	            raise Exception(
	                "Invalid Notification format. " +
	                "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
	
	        self.format = notification_format
	        self.payload = payload
	
	        # array with keynames for headers
	        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
	        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
	        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
	        self.headers = None

Cette classe est un conteneur pour un corps de notification natif ou un ensemble de propriétés dans le cas d’un modèle de notification, et un ensemble d’en-têtes contenant le format (plateforme native ou modèle) et des propriétés spécifiques de la plateforme (telles que la propriété d’expiration d’Apple et les en-têtes WNS).

Pour connaître toutes les options disponibles, reportez-vous à la [documentation sur les API REST de Notification Hubs](http://msdn.microsoft.com/library/dn495827.aspx) et aux formats spécifiques des plateformes de notification.

Avec cette classe, nous pouvons à présent écrire les méthodes d'envoi des notifications à l'intérieur de la classe **NotificationHub**.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

	def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Les méthodes ci-dessus envoient une demande POST HTTP au point de terminaison /messages de votre hub de notification, avec le corps et les en-têtes corrects pour envoyer la notification.

### Utilisation de la propriété debug pour activer la journalisation détaillée
L’activation de la propriété debug lors de l’initialisation du hub de notification permet de rédiger des informations de journalisation détaillées sur le vidage des requêtes et réponses HTTP, ainsi que le résultat détaillé de l’envoi des messages de notification. Nous avons récemment ajouté cette propriété appelée [propriété TestSend Notification Hubs](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx), qui retourne des informations détaillées sur le résultat de l'envoi de notification. Pour l’utiliser, réalisez une initialisation à l’aide de ce qui suit :

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

L’URL HTTP de demande d’envoi de hub de notification est ajoutée avec une chaîne de requête « test ».

##<a name="complete-tutorial"></a>Suivi du didacticiel
Vous pouvez à présent terminer le didacticiel de prise en main en envoyant la notification à partir d’un serveur principal Python.

Initialisez votre client Notification Hubs (remplacez la chaîne de connexion et le nom du hub comme indiqué dans le [didacticiel de prise en main]) :

	hub = NotificationHub("myConnectionString", "myNotificationHubName")

Ajoutez ensuite le code d’envoi en fonction de la plateforme mobile cible. Cet exemple ajoute également des méthodes de plus haut niveau pour activer l’envoi de notifications basées sur la plateforme, par exemple send\_windows\_notification pour Windows, send\_apple\_notification (pour Apple), etc.

### Windows Store et Windows Phone 8.1 (non-Silverlight)

	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Test</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)

### Windows Phone 8.0 et 8.1 Silverlight

	hub.send_mpns_notification(toast)

### iOS

	alert_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_apple_notification(alert_payload)

### Android
	gcm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_gcm_notification(gcm_payload)

### Kindle Fire
	adm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_adm_notification(adm_payload)

### Baidu
	baidu_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_baidu_notification(baidu_payload)

L’exécution de votre code Python produit normalement une notification qui apparaît sur votre appareil cible.

## Exemples :

### Activation de la propriété debug
Quand vous activez l'indicateur de débogage pendant l'initialisation de la classe NotificationHub, vous voyez un vidage détaillé des requêtes et réponses HTTP, ainsi que NotificationOutcome, comme suit, où vous pouvez comprendre quels en-têtes HTTP sont transmis dans la demande et quelle réponse HTTP a été reçue à partir du hub de notification : ![][1]

Vous verrez un résultat détaillé de hub de notification, par exemple

- lorsque le message est envoyé au service de notification Push.
	
		<Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Si aucune cible n’a été trouvée pour les notifications Push, vous verrez probablement les éléments suivants dans la réponse (ce qui indique qu’il n’y a eu aucun enregistrement trouvé pour fournir la notification, probablement parce que certaines balises des inscriptions ne correspondaient pas)

		'<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### Notification toast de diffusion à Windows 

Notez les en-têtes qui sont envoyés lorsque vous envoyez une notification toast de diffusion au client Windows.

	hub.send_windows_notification(wns_payload)

![][2]

### Envoyer une notification indiquant une balise (ou une expression de balise)

Notez l'en-tête HTTP de balises qui est ajouté à la requête HTTP (dans l'exemple ci-dessous, vous envoyez la notification uniquement aux inscriptions avec la charge utile « sports »)

	hub.send_windows_notification(wns_payload, "sports")

![][3]

### Envoyer une notification spécifiant plusieurs balises

Notez comment l’en-tête HTTP des balises change lorsque plusieurs balises sont envoyées.
	
	tags = {'sports', 'politics'}
	hub.send_windows_notification(wns_payload, tags)

![][4]

### Notification avec modèle

Notez que l’en-tête HTTP de format change et que le corps de charge utile est envoyé en tant que partie intégrante du corps de requête HTTP :

**Côté client - modèle enregistré**

		var template =
		                @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Côté serveur - envoi de la charge utile**
		
		template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
		hub.send_template_notification(template_payload)

![][5]


## Étapes suivantes
Dans cette rubrique, nous vous avons montré comment créer un client REST Python simple pour Notification Hubs. À ce stade, vous pouvez :

* télécharger l'intégralité de l'[exemple de wrapper REST Python], qui contient tout le code ci-dessus ;
* poursuivre l'apprentissage de la fonctionnalité de balisage de Notification Hubs dans le [didacticiel Nouvelles de dernière minute] ;
* poursuivre l'apprentissage de la fonctionnalité des modèles de Notification Hubs dans le [didacticiel de localisation des dernières nouvelles].

<!-- URLs -->
[exemple de wrapper REST Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[didacticiel de prise en main]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[didacticiel Nouvelles de dernière minute]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[didacticiel de localisation des dernières nouvelles]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 

<!---HONumber=AcomDC_0706_2016-->