<properties 
	pageTitle="Utilisation des files d'attente Service Bus (.NET) - Azure" 
	description="Découvrez comment utiliser les files d'attente Service Bus dans Azure. Exemples de code écrits en C# à l'aide de l'API .NET." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>





# Utilisation des files d'attente Service Bus

<span>Ce guide vous montre comment utiliser les files d'attente Service Bus. Les exemples sont écrits en C\# et utilisent l'API .NET. Les scénarios couverts dans ce guide sont les suivants : **création de files d'attente, envoi et réception de messages** et **suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## Configuration de l'application pour l'utilisation de Service Bus

Quand vous créez une application qui utilise Service Bus, vous devez
ajouter une référence à l'assembly Service Bus et inclure les
espaces de noms correspondants.

## Obtention du package NuGet Service Bus

Le package **NuGet** Service Bus est le moyen le plus simple de se procurer l'API Service Bus et de configurer votre application avec toutes les dépendances Service Bus. L'extension Visual Studio NuGet facilite l'installation et la mise à jour des bibliothèques et des outils de Visual Studio et Visual Studio Express 2012 pour le Web.

Pour installer le package NuGet dans votre application, procédez comme suit :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis cliquez sur **Gérer les packages NuGet**.
2.  Recherchez " Azure " et sélectionnez l'élément **Azure Service Bus**. Cliquez sur **Installer** pour terminer l'installation, puis fermez cette boîte de dialogue.

    ![][7]

Vous êtes maintenant en mesure d'écrire du code pour Service Bus.


## Configuration d'une chaîne de connexion Service Bus

Service Bus utilise une chaîne de connexion pour stocker les points de terminaison et les informations d'identification. Vous pouvez placer votre chaîne de connexion dans un fichier de configuration, au lieu de la coder en dur dans le code :

- Quand vous utilisez Azure Cloud Services, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers `*.csdef` et`*.cscfg`).
- Quand vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, un fichier `web.config`).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode CloudConfigurationManager.GetSetting, tel qu'indiqué plus loin dans ce guide.

### <a name="config-connstring"> </a>Configuration de votre chaîne de connexion lors de l'utilisation des services cloud

Le mécanisme de configuration de service est propre aux projets de services cloud Azure et vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail de gestion Azure sans avoir à redéployer votre application.  Par exemple, ajoutez un paramètre à votre fichier de définition de service (*.csdef), comme indiqué ci-dessous :

	<ServiceDefinition name="WindowsAzure1">
	...
		<WebRole name="MyRole" vmsize="Small">
	    	<ConfigurationSettings>
	      		<Setting name="Microsoft.ServiceBus.ConnectionString" />
    		</ConfigurationSettings>
  		</WebRole>
	...
	</ServiceDefinition>

Vous devez ensuite spécifier des valeurs dans le fichier de configuration de service (*.cscfg) :

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

Quand vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé d'utiliser le système de configuration .NET (par exemple, web.config).  Stockez la chaîne de connexion en utilisant l'élément <appSettings> :

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Utilisez l'émetteur et les valeurs de clé récupérées sur le portail de gestion, comme décrit dans la section précédente.

## Création d'une file d'attente

Vous pouvez effectuer des opérations de gestion pour les files d'attente Service Bus via la classe **NamespaceManager**. La classe **NamespaceManager** fournit des méthodes pour créer, énumérer et supprimer des files d'attente. 

Dans cet exemple, un objet **NamespaceManager** est construit à l'aide de la classe Azure **CloudConfigurationManager** avec une chaîne de connexion constituée de l'adresse de base d'un espace de noms de service Service Bus et des informations d'identification appropriées assorties d'autorisations pour les gérer. Cette chaîne de connexion se présente comme suit : 

	Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

Voici un exemple reprenant les paramètres de configuration de la section précédente :

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

## Envoi de messages à une file d'attente

Pour envoyer un message à une file d'attente Service Bus, votre application crée un objet **QueueClient** en utilisant la chaîne de connexion.

Le code ci-dessous montre comment créer un objet **QueueClient**
pour la file d'attente " TestQueue " créée ci-dessus à l'aide de l'appel d'API **CreateFromConnectionString** :

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
		QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

	Client.Send(new BrokeredMessage());

Les messages envoyés aux files d'attente Service Bus (et ceux en provenance de celles-ci) sont des instances de la classe **BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de propriétés standard (telles que **Label** et **TimeToLive**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires. Une application peut définir le corps du message en transmettant un objet sérialisable au constructeur de l'objet **BrokeredMessage** ; le sérialiseur **DataContractSerializer** approprié est alors utilisé pour sérialiser l'objet. Une autre possibilité consiste à fournir un **System.IO.Stream**.  L'exemple suivant montre comment envoyer cinq messages de test au client **QueueClient** " TestQueue " obtenu dans l'extrait de code précédent :

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

## Réception des messages d'une file d'attente

Le moyen le plus simple de recevoir les messages d'une file d'attente est d'utiliser un objet **QueueClient**. Ces objets peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : quand Service Bus reçoit une demande de lecture pour un message présent dans une file d'attente, il marque le message comme étant consommé et le renvoie à l'application. Le mode **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

En mode **PeekLock** (par défaut), la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant **Complete** pour le message reçu. Quand il obtient l'appel **Complete**, Service Bus marque le message comme étant consommé et le supprime de la file d'attente.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités avec le mode par défaut **PeekLock**. Pour spécifier une valeur différente pour **ReceiveMode**, vous pouvez utiliser une autre surcharge pour **CreateFromConnectionString**. Dans cet exemple, une boucle infinie est créée et les messages sont traités à mesure qu'ils parviennent à la file d'attente " TestQueue " :

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

## Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **Abandon** pour le message reçu (au lieu de la méthode **Complete**). Cela amène Service Bus à déverrouiller le message dans la file d'attente et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la demande **Complete**, le message est à nouveau remis à l'application au moment où elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois, mais, dans certains cas, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, Pour ce faire, il suffit souvent d'utiliser la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

## Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces
liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements.][]
-   Créez une application opérationnelle capable d'envoyer et de recevoir des messages à destination et
 en provenance d'une file d'attente Service Bus : [Didacticiel .NET sur la messagerie répartie Service Bus].

  [Étapes suivantes]: #next-steps
  [Présentation des files d'attente Service Bus]: #what-queues
  [Création d'un espace de noms de service]: #create-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-creds
  [Configuration de votre application pour l'utilisation de Service Bus]: #configure-app
  [Procédure : configuration d'une chaîne de connexion Service Bus]: #set-up-connstring
  [Procédure : configuration de votre chaîne de connexion]: #config-connstring
  [Procédure : création d'une file d'attente]: #create-queue
  [Procédure : envoi de messages à une file d'attente]: #send-messages
  [Procédure : réception des messages d'une file d'attente]: #receive-messages
  [Procédure : gestion des blocages d'application et des messages illisibles]: #handle-crashes
  [Concepts liés aux files d'attente]: ./media/service-bus-dotnet-how-to-use-queues/sb-queues-08.png
  [Portail de gestion Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
  [Didacticiel .NET sur la messagerie répartie Service Bus]: http://msdn.microsoft.com/library/windowsazure/hh367512.aspx

<!--HONumber=35.1-->

<!--HONumber=46--> 
