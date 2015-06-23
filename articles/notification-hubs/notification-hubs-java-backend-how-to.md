<properties 
	pageTitle="Utilisation de Notification Hubs avec Java" 
	description="Découvrez comment utiliser Azure Notification Hubs à partir d'un serveur principal Java." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="java" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="01/12/2015" 
	ms.author="yuaxu"/>

# Utilisation de Notification Hubs à partir de Java
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js">Node.js</a>
</div>

Cette rubrique décrit les principales fonctionnalités du nouveau kit de développement logiciel (SDK) Java officiel pour Azure Notification Hub, entièrement pris en charge. 
Il s'agit d'un projet open source et vous pouvez afficher tout le code du Kit de développement logiciel (SDK) sur [Java SDK]. 

Vous pouvez accéder à toutes les fonctionnalités de Notification Hubs à partir d'un serveur principal Java/PHP/Python/Ruby en utilisant l'interface REST des concentrateurs de notification, comme décrit dans la rubrique MSDN [API REST de Notification Hubs](http://msdn.microsoft.com/library/dn223264.aspx). Ce kit de développement logiciel Java fournit un wrapper fin par rapport aux interfaces REST dans Java. 

Le kit de développement logiciel (SDK) prend actuellement en charge :

- CRUD sur Notification Hubs 
- CRUD sur les inscriptions
- Gestion de l'installation
- Importation/exportation des inscriptions
- Envois réguliers
- Envois planifiés
- Opérations asynchrones via Java NIO
- Plateformes prises en charge : APNs (iOS), GCM (Android), WNS (applications Windows Store), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android sans services Google) 

## Utilisation du kit de développement logiciel (SDK)

### Compilation et génération

Utilisation de [Maven]

Génération de :

	mvn package

## Code

### CRUD de Notification Hub

**Création d'un NamespaceManager :**
	
	NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Création d'un concentrateur de notification :**
	
	NotificationHubDescription hub = new NotificationHubDescription("hubname");
	hub.setWindowsCredential(new WindowsCredential("sid","key"));
	hub = namespaceManager.createNotificationHub(hub);
	
 OU

	hub = new NotificationHub("connection string", "hubname");

**Obtention d'un concentrateur de notification :**
	
	hub = namespaceManager.getNotificationHub("hubname");

**Mise à jour d'un concentrateur de notification :**
	
	hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
	hub = namespaceManager.updateNotificationHub(hub);

**Suppression d'un concentrateur de notification :**
	
	namespaceManager.deleteNotificationHub("hubname");

### CRUID d'inscription
**Création d'un client de concentrateur de notification :**

	hub = new NotificationHub("connection string", "hubname");

**Création d'une inscription Windows :**

	WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");    
	hub.createRegistration(reg);

**Création d'une inscription iOS :**

	AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.createRegistration(reg);

De même, vous pouvez créer des inscriptions pour Android (GCM), Windows Phone (MPNS) et Kindle Fire (ADM).

**Création de modèles d'inscription :**

	WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
	reg.getHeaders().put("X-WNS-Type", "wns/toast");
	hub.createRegistration(reg);

**Création d'inscriptions à l'aide du modèle registrationid + upsert**

Supprime les doublons dus à des réponses perdues, si les ID d'inscription sont stockées sur le périphérique :

	String id = hub.createRegistrationId();
	WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
	hub.upsertRegistration(reg);

**Mise à jour d'inscriptions :**
	
	hub.updateRegistration(reg);

**Suppression d'inscriptions :**
	
	hub.deleteRegistration(regid);

**Interrogation d'inscriptions :**

* 	**Obtention d'une inscription unique :**
	
		hub.getRegistration(regid);
	
* 	**Obtention de toutes les inscriptions du concentrateur :**
	
		hub.getRegistrations();
	
* 	**Obtention des inscriptions avec balise :**
	
		hub.getRegistrationsByTag("myTag");
	
* 	**Obtention des inscriptions par canal :**
	
		hub.getRegistrationsByChannel("devicetoken");

Toutes les requêtes de collection prennent en charge les jetons $top et de continuation.

### Utilisation de l'API d'installation
L'API d'installation est un autre mécanisme de gestion des inscriptions. Au lieu de maintenir plusieurs inscriptions, ce qui n'est pas simple et peut facilement être mal effectué, il est possible d'utiliser un objet d'Installation UNIQUE. 
L'installation contient tout ce dont vous avez besoin : un canal Push (jeton de périphérique), des balises, des modèles, des vignettes secondaires (pour WNS et APN). Vous n'avez plus besoin d'appeler le service pour obtenir l'Id : il suffit de générer un GUID ou tout autre identificateur, de le conserver sur le périphérique et de l'envoyer à votre serveur principal avec le canal Push (jeton de périphérique). 
Sur le serveur principal, effectuez simplement un appel unique : CreateOrUpdateInstallation est entièrement idempotent. N'hésitez pas à réessayer si nécessaire.

L'exemple pour Amazon Kindle Fire ressemble à ceci :

	Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
	hub.createOrUpdateInstallation(installation);

Si vous souhaitez le mettre à jour : 

	installation.addTag("foo");
	installation.addTemplate("template1", new InstallationTemplate("{"data":{"key1":"$(value1)"}}","tag-for-template1"));
	installation.addTemplate("template2", new InstallationTemplate("{"data":{"key2":"$(value2)"}}","tag-for-template2"));
	hub.createOrUpdateInstallation(installation);

pour des scénarios avancés, nous avons des capacités de mise à jour partielle qui permettent de modifier uniquement des propriétés particulières de l'objet d'installation. La mise à jour partielle est un sous-ensemble d'opérations de correction JSON que vous pouvez exécuter sur l'objet de l'Installation.

	PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
	PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
	PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{"data":{"key3":"$(value3)"}}","tag-for-template1")).toJson());
	hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Supprimer l'Installation :

	hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate, Patch et Delete sont cohérents avec Get. L'opération demandée va simplement à la file d'attente système lors de l'appel et est exécutée en arrière-plan. Notez que Get n'est pas conçu pour le scénario d'exécution principal, mais uniquement pour le débogage et le dépannage. Il est étroitement limité par le service.

Le flux d'envoi pour les Installations est identique à celui des Inscriptions. Nous venons de créer une option pour la cible de notification de l'Installation particulière : utilisez simplement la balise "InstallationId:{desired-id}". Pour l'exemple ci-dessus, elle ressemblerait à ceci :

	Notification n = Notification.createWindowsNotification("WNS body");
	hub.sendNotification(n, "InstallationId:{installation-id}");

Pour l'un des modèles :

	Map<String, String> prop =  new HashMap<String, String>();
	prop.put("value3", "some value");
	Notification n = Notification.createTemplateNotification(prop);
	hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### planification des notifications (disponible pour le niveau STANDARD)

Similaire à l'envoi ordinaire, mais avec un paramètre supplémentaire : scheduledTime indique quand la notification doit être remise. Le service accepte n'importe quel point dans le temps entre maintenant + 5 minutes et maintenant + 7 jours.

**Planification d'une notification native Windows :**

	Calendar c = Calendar.getInstance();
	c.add(Calendar.DATE, 1);    
	Notification n = Notification.createWindowsNotification("WNS body");
	hub.scheduleNotification(n, c.getTime());

### Importation/exportation (disponible pour le niveau STANDARD)
Il est parfois nécessaire d'effectuer une opération en bloc vis-à-vis des inscriptions. C'est généralement le cas pour l'intégration avec un autre système ou simplement pour un correctif massif pour mettre à jour les balises par exemple. Il est fortement déconseillé d'utiliser des flux Get/Update si cela concerne des milliers d'inscriptions. La fonction d'importation/exportation est conçue pour couvrir ce scénario. Vous fournissez un accès à un conteneur d'objets blob dans votre compte de stockage en tant que source de données entrantes et emplacement de sortie.

**Envoi du travail d'exportation :**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ExportRegistrations);
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);


**Envoi du travail d'importation :**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
	job.setImportFileUri("input file uri with SAS signature");
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);

**Attendez que la tâche soit terminée :**

	while(true){
	    Thread.sleep(1000);
	    job = hub.getNotificationHubJob(job.getJobId());
	    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
	        break;
	}       

**Obtenez tous les travaux :**

	List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**URI avec signature SAS :**
C'est l'URL d'un fichier blob ou d'un conteneur d'objets blob et d'un jeu de paramètres tels que les autorisations et l'heure d'expiration, ainsi que la signature de toutes ces opérations effectuées à l'aide de la clé SAS du compte. Le Kit de développement logiciel (SDK) Azure Storage Java dispose de nombreuses fonctionnalités, notamment la création de ce type d'URI. Vous pouvez également examiner la classe de test ImportExportE2E (à partir de l'emplacement github) qui possède une implémentation très basique et compacte de l'algorithme de signature.

###Envoi de notifications
L'objet de Notification est simplement un corps avec des en-têtes. Certaines méthodes utilitaires vous aident à créer les objets de notifications natives et les modèles.

* **Windows Store et Windows Phone 8.1 (non Silverlight)**

		String toast = "<toast><visual><binding template="ToastText01"><text id="1">Hello from Java!</text></binding></visual></toast>";
		Notification n = Notification.createWindowsNotification(toast);
		hub.sendNotification(n);

* **iOS**

		String alert = "{"aps":{"alert":"Hello from Java!"}}";
		Notification n = Notification.createAppleNotification(alert);
		hub.sendNotification(n);

* **Android**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createGcmNotification(message);
		hub.sendNotification(n);

* **Windows Phone 8.0 et 8.1 Silverlight**

		String toast = "<?xml version="1.0" encoding="utf-8"?>" +
			        "<wp:Notification xmlns:wp="WPNotification">" +
			           "<wp:Toast>" +
			                "<wp:Text1>Hello from Java!</wp:Text1>" +
			           "</wp:Toast> " +
			        "</wp:Notification>";
		Notification n = Notification.createMpnsNotification(toast);
		hub.sendNotification(n);

* **Kindle Fire**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createAdmNotification(message);
		hub.sendNotification(n);

* **Envoi à des balises**

		Set<String> tags = new HashSet<String>();
		tags.add("boo");
		tags.add("foo");
		hub.sendNotification(n, tags);

* **Envoi à l'expression de balise**       

		hub.sendNotification(n, "foo && ! bar");

* **Envoyer une notification modèle**

		Map<String, String> prop =  new HashMap<String, String>();
		prop.put("prop1", "v1");
		prop.put("prop2", "v2");
		Notification n = Notification.createTemplateNotification(prop);
		hub.sendNotification(n);

L'exécution de votre code Java produit normalement une notification qui apparaît sur votre appareil cible.

##<a name="next-steps"></a>Étapes suivantes
Dans cette rubrique, nous vous avons montré comment créer un client REST Java simple pour Notification Hubs. À ce stade, vous pouvez :

* Téléchargez la version complète du [Kit de développement logiciel (SDK) Java], qui contient l'ensemble du code du Kit de développement logiciel (SDK). 
* Entraînez-vous avec les exemples :
	- [Prise en main de Notification Hubs]
	- [Envoi des dernières nouvelles]
	- [Envoi de dernières nouvelles localisées]
	- [Envoi de notifications aux utilisateurs authentifiés]
	- [Envoi de notifications multiplateforme aux utilisateurs authentifiés]

[Kit de développement logiciel (SDK) Java]: https://github.com/Azure/azure-notificationhubs-java-backend
[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Didacticiel de prise en main]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Prise en main de Notification Hubs]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[Envoi des dernières nouvelles]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[Envoi de dernières nouvelles localisées]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Envoi de notifications aux utilisateurs authentifiés]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[Envoi de notifications multiplateforme aux utilisateurs authentifiés]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/

<!--HONumber=49--> 