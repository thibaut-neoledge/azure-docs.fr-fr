<properties
    pageTitle="Utilisation des files d’attente Service Bus (.NET) | Microsoft Azure"
	description="Découvrez comment utiliser les files d'attente Service Bus dans Azure. Exemples de code écrits en C# à l’aide de l’API .NET."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
    ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="07/02/2015"
	ms.author="sethm"/>

# Utilisation des files d’attente Service Bus

Cet article décrit l’utilisation des files d’attente Service Bus. Les exemples sont écrits en C# et utilisent l'API .NET. Les scénarios abordés dans ce guide incluent la création de files d’attente et l’envoi et la réception de messages. Pour plus d’informations sur les files d’attente, consultez la section [Étapes suivantes](#Next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Configuration de l'application pour l'utilisation de Service Bus

Lorsque vous créez une application qui fait appel à Service Bus, vous devez ajouter une référence à l'assembly Service Bus et inclure les espaces de noms correspondants.

## Ajout du package NuGet Service Bus

Le package **NuGet** Service Bus est le moyen le plus simple de se procurer l'API Service Bus et de configurer votre application avec toutes les dépendances Service Bus. L'extension Visual Studio NuGet facilite l'installation et la mise à jour des bibliothèques et des outils de Visual Studio et Visual Studio Express. Le package NuGet Service Bus est le moyen le plus simple de se procurer l'API Service Bus et de configurer votre application avec toutes les dépendances Service Bus.

Pour installer le package NuGet dans votre application, procédez comme suit :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **References**, puis cliquez sur **Manage NuGet Packages**.
2.  Recherchez « Service Bus » et sélectionnez l’élément **Microsoft Azure Service Bus**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.

    ![][7]

Vous êtes maintenant en mesure d’écrire du code pour Service Bus.

## Configuration d’une chaîne de connexion Service Bus

Service Bus utilise une chaîne de connexion pour stocker les points de terminaison et les informations d’identification. Vous pouvez placer votre chaîne de connexion dans un fichier de configuration, au lieu de la coder en dur :

- Quand vous utilisez Azure Cloud Services, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers .csdef et .cscfg).
- Quand vous utilisez les sites Web Azure ou Microsoft Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, le fichier Web.config).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode `CloudConfigurationManager.GetSetting`, comme indiqué plus loin dans cet article.

### Configuration de votre chaîne de connexion lors de l'utilisation des services cloud

Le mécanisme de configuration de service est propre aux projets de services cloud Azure et vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail Azure sans avoir à redéployer votre application. Par exemple, ajoutez une étiquette `Setting` à votre fichier de définition de service (.csdef), comme indiqué dans l’exemple qui suit :

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Vous devez ensuite spécifier des valeurs dans le fichier de configuration de service (.cscfg), comme indiqué dans l’exemple qui suit :

    <ServiceConfiguration serviceName="Azure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString"
                         value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Utilisez le nom de clé et les valeurs de clé de signature d’accès partagé (SAP) récupérés sur le portail Azure, comme dans la section précédentdécrite.

### Configuration de votre chaîne de connexion dans le cadre de l’utilisation de sites web Azure ou de machines virtuelles Azure

Quand vous utilisez sites web ou Virtual Machines, il est recommandé d’utiliser le système de configuration .NET (par exemple, **Web.config**). Stockez la chaîne de connexion en utilisant l’élément `<appSettings>` :

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Utilisez le nom et les valeurs de clé SAP que vous avez récupérées sur le portail Azure, comme décrit dans la section précédente.

## Création d’une file d’attente

Vous pouvez effectuer des opérations de gestion pour les files d’attente Service Bus en utilisant la [classe `NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Cette classe fournit des méthodes pour créer, énumérer et supprimer des files d’attente.

Dans cet exemple, un objet `NamespaceManager` est réalisé à l’aide de la classe Azure `CloudConfigurationManager` avec une chaîne de connexion constituée de l’adresse de base d’un espace de noms de service Service Bus et des informations d’identification SAP appropriées assorties d’autorisations pour les gérer. Cette chaîne de connexion respecte le format indiqué dans l’exemple suivant.

    Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey

Utilisez l’exemple suivant en reprenant les paramètres de configuration de la section précédente.

    // Create the queue if it does not exist already.
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Les surcharges de la méthode [`CreateQueue`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) vous permettent de paramétrer les propriétés de la file d’attente (par exemple, pour faire en sorte que la valeur par défaut de durée de vie (« time-to-live ») soit appliquée aux messages envoyés à la file d’attente). Ces paramètres sont appliqués à l’aide de la classe [`QueueDescription`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). L’exemple suivant montre comment créer une file d’attente nommée « TestQueue » avec une taille maximale de 5 Go et une durée de vie de message par défaut d’une minute.

    // Configure queue settings.
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new queue with custom settings.
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

> [AZURE.NOTE]Vous pouvez utiliser la méthode [`QueueExists`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) sur les objets [`NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) pour vérifier s’il existe déjà une file d’attente d’un nom déterminé dans un espace de noms de service.

## Envoi de messages à une file d’attente

Pour envoyer un message à une file d’attente de Service Bus, votre application crée un objet [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) en utilisant la chaîne de connexion.

Le code qui suit montre comment créer un objet [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) pour la file d’attente « TestQueue » que vous venez de créer à l’aide de l’appel d’API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx).

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client =
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

Les messages envoyés aux files d’attente Service Bus (et reçus de celles-ci) sont des instances de la classe [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Les objets `BrokeredMessage` possèdent un ensemble de propriétés standard (telles que [`Label`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et [`TimeToLive`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu’un corps de données d’application arbitraires. Une application peut définir le corps du message en transmettant un objet sérialisable au constructeur de l’objet[`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) et le sérialiseur **DataContractSerializer** approprié est alors utilisé pour sérialiser l’objet. Une autre possibilité consiste à fournir un **System.IO.Stream**.

L’exemple suivant montre comment envoyer cinq messages de test à l’objet « TestQueue » [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) obtenu dans l’extrait de code précédent :

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body.
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties.
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;

       // Send message to the queue.
       Client.Send(message);
     }

Les files d’attente Service Bus prennent en charge [une taille de message maximale de 256 Ko](service-bus-quotas.md) (l’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go. Si le partitionnement est activé, la limite supérieure est plus élevée. Pour plus d’informations, consultez la rubrique [Partitionnement des entités de messagerie](https://msdn.microsoft.com/library/azure/dn520246.aspx).

## Réception des messages d’une file d’attente

La méthode recommandée pour recevoir les messages d’une file d’attente consiste à utiliser un objet [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Les objets `QueueClient` peuvent fonctionner dans deux modes différents : [`ReceiveAndDelete` et `PeekLock`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Quand le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique. En effet, quand Service Bus reçoit une demande de lecture pour un message figurant dans une file d’attente, il marque le message comme consommé et le renvoie à l’application. **ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d’un message en cas d’échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l’application redémarre et recommence à consommer des messages, elle manque le message consommé avant l’incident.

En mode **PeekLock** (par défaut), la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Une fois que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière sûre pour le traiter par la suite), elle effectue la deuxième étape du processus de réception en appelant [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. Quand Service Bus obtient l’appel `Complete`, il marque le message comme étant consommé et le supprime de la file d’attente.

L’exemple suivant montre comment les messages peuvent être reçus et traités à l’aide du mode par défaut **PeekLock**. Pour spécifier une valeur [`ReceiveMode`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) différente, vous pouvez utiliser une autre surcharge de [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx). Cet exemple utilise le rappel [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) pour traiter les messages dès leur arrivée dans **TestQueue**.

    string connectionString =
      CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    QueueClient Client =
      QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    // Configure the callback options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    // Callback to handle received messages.
    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from queue.
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Test Property: " +
            message.Properties["TestProperty"]);

            // Remove message from queue.
            message.Complete();
        }
            catch (Exception)
        {
            // Indicates a problem, unlock message in queue.
            message.Abandon();
        }
    }, options);

Cet exemple configure le rappel [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) à l’aide d’un objet [`OnMessageOptions`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [`AutoComplete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) est défini sur **false** pour permettre un contrôle manuel sur le moment de décider où le message reçu est [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx). [`AutoRenewTimeout`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) est défini sur 1 minute, ce qui oblige le client à attendre jusqu’à une minute l’arrivée d’un message avant que l’appel expire et que le client effectue un nouvel appel pour consulter ses messages. La valeur de cette propriété permet de réduire le nombre de fois où le client effectue des appels facturables qui ne récupèrent aucun message.

## Gestion des blocages d’application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application de réception ne parvient pas à traiter le message pour une raison quelconque, elle peut appeler la méthode [`Abandon`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) pour le message reçu (au lieu de la méthode [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu’un message verrouillé dans une file d’attente est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l’application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l’application subit un incident après le traitement du message, mais avant l’émission de la demande [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), le message est à nouveau remis à l’application lorsqu’elle redémarre. Cette opération est souvent appelée **Au moins une fois**. Chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété [`MessageId`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) du message qui est constante pendant les tentatives de remise.

## Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la présentation MSDN suivante : [Files d’attente, rubriques et abonnements][].
-   Créez une application opérationnelle, capable d’envoyer et de recevoir des messages à destination et en provenance d’une file d’attente Service Bus : [Didacticiel .NET sur la messagerie répartie Service Bus].
-   Exemples Service Bus : téléchargement depuis la page [Exemples Azure][] ou consultation de la présentation sur [MSDN][].

  [What are Service Bus Queues]: #what-queues
  [Create a Service Namespace]: #create-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-creds
  [Configure Your Application to Use Service Bus]: #configure-app
  [How to: Set Up a Service Bus Connection String]: #set-up-connstring
  [How to: Configure your Connection String]: #config-connstring
  [How to: Create a Queue]: #create-queue
  [How to: Send Messages to a Queue]: #send-messages
  [How to: Receive Messages from a Queue]: #receive-messages
  [How to: Handle Application Crashes and Unreadable Messages]: #handle-crashes
  [Azure portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Files d’attente, rubriques et abonnements]: http://msdn.microsoft.com/library/azure/hh367516.aspx
  [Didacticiel .NET sur la messagerie répartie Service Bus]: http://msdn.microsoft.com/library/azure/hh367512.aspx
  [Exemples Azure]: https://code.msdn.microsoft.com/windowsazure/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [MSDN]: https://msdn.microsoft.com/library/azure/dn194201.aspx

<!---HONumber=August15_HO9-->