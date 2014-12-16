<properties urlDisplayName="Service Bus Queues" pageTitle="Utilisation des files d'attente de Service Bus (.NET) - Azure " metaKeywords=" files d'attente du Service Bus Azure, files d'attente Azure, messagerie Azure, files d'attente Azure C#, files d'attente Azure .NET" description="Learn how to use Service Bus queues in Azure. Code samples written in C# using the .NET API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />





# Utilisation des files d'attente Service Bus

<span>Ce guide vous montre comment utiliser les files d'attente Service Bus. Les exemples sont écrits en C\# et utilisent l'API .NET. Les scénarios couverts dans ce guide sont les suivants : **création de files d'attente, envoi et réception de messages** et **suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

<h2>Configuration de l'application pour l'utilisation de Service Bus</h2>

Lorsque vous créez une application qui fait appel à Service Bus, vous devez ajouter une référence à l'assembly Service Bus et inclure les espaces de noms correspondants.

<h2>Récupération du package NuGet Service Bus</h2>

Le package **NuGet** Service Bus est le moyen le plus simple de se procurer l'API Service Bus et de configurer votre application avec toutes les dépendances Service Bus. L'extension Visual Studio NuGet facilite l'installation et la mise à jour des bibliothèques et des outils de Visual Studio et Visual Studio Express 2012 pour le web.

Pour installer le package NuGet dans votre application, procédez comme suit :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis cliquez sur
    **Gérer les packages NuGet**.
2.  Recherchez " WindowsAzure " et sélectionnez l'élément **Azure
    Service Bus**. Cliquez sur **Installer** pour terminer l'installation,
    puis fermez cette boîte de dialogue.

    ![][7]

Vous êtes maintenant en mesure d'écrire du code pour Service Bus.

<h2>Configuration d'une chaîne de connexion de Service Bus</h2>

Service Bus utilise une chaîne de connexion pour stocker les points de terminaison et les informations d'identification. Vous pouvez placer votre chaîne de connexion dans un fichier de configuration au lieu de la coder en dur dans le code :

- Lorsque vous utilisez Azure Cloud Services, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers " *.csdef " et " *.cscfg ").
- Lorsque vous utilisez Azure Websites ou Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, un fichier " web.config ").

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode " CloudConfigurationManager.GetSetting ", comme cela est présenté plus loin dans ce guide.

### <a name="config-connstring"> </a>Configuration de votre chaîne de connexion lors de l'utilisation des services cloud

Le mécanisme de configuration de service est propre aux projets d'Azure Cloud Services et vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail de gestion Azure sans avoir à redéployer votre application.  Par exemple, ajoutez un paramètre à votre fichier de définition de service (" *.csdef "), comme indiqué ci-dessous :

	<ServiceDefinition name="WindowsAzure1">
	...
		<WebRole name="MyRole" vmsize="Small">
	    	<ConfigurationSettings>
	      		<Setting name="Microsoft.ServiceBus.ConnectionString" />
    		</ConfigurationSettings>
  		</WebRole>
	...
	</ServiceDefinition>

You then specify values in the service configuration (`*.cscfg`) file:

	<ServiceConfiguration serviceName="WindowsAzure1">
	...
		<Role name="MyRole">
			<ConfigurationSettings>
				<Setting name="Microsoft.ServiceBus.ConnectionString" 
						 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
			</ConfigurationSettings>
		</Role>
	...
	</ServiceConfiguration>

Utilisez l'émetteur et les valeurs de clé récupérées sur le portail de gestion, comme décrit dans la section précédente.

### Configuration de votre chaîne de connexion dans le cadre de l'utilisation de Sites Web Azure ou Azure Virtual Machines 

Lorsque vous utilisez Websites ou Virtual Machines, il est recommandé d'utiliser le système de configuration .NET (par exemple, " web.config ").  Stockez la chaîne de connexion en utilisant l'élément " <appSettings> " :

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Utilisez l'émetteur et les valeurs de clé récupérées sur le portail de gestion, comme décrit dans la section précédente.

<h2>Création d'une file d'attente</h2>

Vous pouvez effectuer des opérations de gestion pour les files d'attente de Service Bus via la classe **NamespaceManager**. La classe **NamespaceManager** fournit des méthodes pour créer, énumérer et supprimer des files d'attente. 

Dans cet exemple est créé un objet **NamespaceManager** utilisant la classe Azure **CloudConfigurationManager** avec une chaîne de connexion constituée de l'adresse de base d'un espace de noms de service Service Bus et des informations d'identification appropriées assorties d'autorisations pour les gérer. Cette chaîne de connexion se présente comme suit :

	Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

For example, given the configuration settings in the previous section:

	// Create the queue if it does not exist already
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Les surcharges de la méthode **CreateQueue** vous permettent de paramétrer les propriétés de la file d'attente (par exemple, pour faire en sorte que la valeur par défaut de durée de vie (" time-to-live ") soit appliquée aux messages envoyés à la file d'attente). Ces paramètres sont appliqués à l'aide de la classe **QueueDescription**. L'exemple suivant montre comment créer une file d'attente nommée " TestQueue " avec une taille maximale de 5 Go et une durée de vie de message par défaut d'une minute :

	// Configure Queue Settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

	// Create a new Queue with custom settings
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

**Remarque :** vous pouvez utiliser la méthode **QueueExists** sur les objets **NamespaceManager** pour vérifier s'il existe déjà une file d'attente d'un nom déterminé dans un espace de noms de service.

<h2>Envoi de messages à une file d'attente</h2>

Pour envoyer un message à une file d'attente de Service Bus, votre application crée un objet **QueueClient** en utilisant la chaîne de connexion.

Le code suivant montre comment créer un objet **QueueClient** pour la file d'attente " TestQueue " créée ci-dessus en utilisant l'appel API **CreateFromConnectionString** :

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
		QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

	Client.Send(new BrokeredMessage());

Les messages envoyés aux files d'attente de Service Bus (et en provenance de celles-ci) sont des instances de la classe **BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de propriétés standard (telles que **Label** et **TimeToLive**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires. Une application peut définir le corps du message en transmettant un objet sérialisable au constructeur de l'objet **BrokeredMessage** ; le sérialiseur **DataContractSerializer** approprié est alors utilisé pour sérialiser l'objet. Une autre possibilité consiste à fournir un **System.IO.Stream**.

L'exemple suivant montre comment envoyer cinq messages de test au client **QueueClient** " TestQueue " obtenu dans l'extrait de code précédent :

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;   

       // Send message to the queue
       Client.Send(message);
     }

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go.

<h2>Réception des messages d'une file d'attente</h2>

Le moyen le plus simple pour recevoir les messages d'une file d'attente est d'utiliser un objet **QueueClient**. Ces objets peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : quand Service Bus reçoit une demande de lecture pour un message de la file d'attente, il marque le message comme étant consommé et le renvoie à l'application. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

En mode **PeekLock** (mode par défaut), la réception devient une opération en deux étapes qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant **Complete** pour le message reçu. Lorsque Service Bus obtient l'appel **Complete**, il marque le message comme étant consommé et le supprime de la file d'attente.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités à l'aide du mode par défaut **PeekLock**. Pour spécifier une valeur différente pour **ReceiveMode**, vous pouvez utiliser une autre surcharge pour **CreateFromConnectionString**. Dans cet exemple, une boucle infinie est créée et les messages sont traités à mesure qu'ils parviennent à la file d'attente " TestQueue " :

    Client.Receive();
     
    // Continuously process messages sent to the "TestQueue" 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("Test Property: " + 
				message.Properties["TestProperty"]);

             // Remove message from queue
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in queue
             message.Abandon();
          }
       }
    } 

<h2>Gestion des blocages d'application et des messages illisibles</h2>
 Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **Abandon** pour le message reçu (au lieu de la méthode **Complete**). Cela amène Service Bus à déverrouiller le message dans la file d'attente et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la demande **Complete**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

<h2>Étapes suivantes</h2>

Maintenant que vous avez appris les notions de base des files d'attente de Service Bus, consultez ces
liens pour en savoir plus.

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements.][]
-   Créez une application opérationnelle qui envoie et reçoit des messages vers et
    à partir d'une file d'attente de Service Bus : [Didacticiel .NET sur la messagerie répartie
    Service Bus].

  [Étapes suivantes]: #next-steps
  [Présentation des files d'attente de Service Bus]: #what-queues
  [Création d'un espace de noms de service]: #create-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-creds
  [Configuration de votre application pour l'utilisation de Service Bus]: #configure-app
  [ Configuration d'une chaîne de connexion de Service Bus]: #set-up-connstring
  [ configurer votre chaîne de connexion]: #config-connstring
  [ Création d'une file d'attente]: #create-queue
  [ Envoi de messages à une file d'attente]: #send-messages
  [ Réception des messages d'une file d'attente]: #receive-messages
  [ Gestion des blocages d'application et des messages illisibles]: #handle-crashes
  [Concepts de la file d'attente]: ./media/service-bus-dotnet-how-to-use-queues/sb-queues-08.png
  [Portail de gestion Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
  [Didacticiel .NET sur la messagerie répartie Service Bus]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367512.aspx
