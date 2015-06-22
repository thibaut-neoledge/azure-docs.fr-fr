<properties 
    pageTitle="Utilisation des rubriques Service Bus (.NET) - Azure" 
    description="Découvrez comment utiliser les rubriques et abonnements Service Bus dans Azure. Les exemples de code sont écrits pour les applications .NET." 
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
    ms.date="02/26/2015" 
    ms.author="sethm"/>





# Utilisation des rubriques/abonnements Service Bus

Ce guide vous montre comment utiliser les rubriques et les abonnements Service Bus. Les exemples sont écrits en C# et utilisent l'API .NET. Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements, création de filtres d'abonnement, envoi de messages** à une rubrique, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**. Pour plus d'informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes][].

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Configuration de l'application pour l'utilisation de Service Bus

Lorsque vous créez une application qui fait appel à Service Bus, vous devez ajouter une référence à l'assembly Service Bus et inclure les espaces de noms correspondants.

## Obtention du package NuGet Service Bus

Le package **NuGet** Service Bus est le moyen le plus simple de se procurer l'API Service Bus et de configurer votre application avec toutes les dépendances Service Bus. L'extension Visual Studio NuGet facilite l'installation et la mise à jour des bibliothèques et des outils de Visual Studio et Visual Studio Express. Le package NuGet Service Bus est le moyen le plus simple de se procurer l'API Service Bus et de configurer votre application avec toutes les dépendances Service Bus.

Pour installer le package NuGet dans votre application, procédez comme suit :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis cliquez sur
    **Gérer les packages NuGet**.
2.  Recherchez " Service Bus" et sélectionnez l'élément **Microsoft Azure Service Bus**. Cliquez sur **Installer** pour terminer l'installation, puis fermez cette boîte de dialogue.

    ![][7]

Vous êtes maintenant en mesure d'écrire du code pour Service Bus.

## Configuration d'une chaîne de connexion Service Bus

Service Bus utilise une chaîne de connexion pour stocker les points de terminaison et les informations d'identification. Vous pouvez placer votre chaîne de connexion dans un fichier de configuration, au lieu de la coder en dur dans le code :

- Lorsque vous utilisez des services cloud Azure, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers `*.csdef` et `*.cscfg`).
- Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, un fichier `web.config`).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode `CloudConfigurationManager.GetSetting`, tel qu'indiqué plus loin dans ce guide.

### Configuration de votre chaîne de connexion lors de l'utilisation des services cloud

Le mécanisme de configuration de service est propre aux projets de services cloud Azure et vous permet de modifier les paramètres de configuration de façon dynamique à partir du portail de gestion Azure sans avoir à redéployer votre application.  Par exemple, ajoutez un paramètre à votre fichier de définition de service (`*.csdef`), comme indiqué ci-dessous :

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Vous devez ensuite spécifier des valeurs dans le fichier de configuration de service (`*.cscfg`) :

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

Utilisez les valeurs de clé récupérées sur le portail de gestion, comme décrit dans la section précédente.

### Configuration de votre chaîne de connexion dans le cadre de l'utilisation de Sites Web Azure ou Azure Virtual Machines

Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé d'utiliser le système de configuration .NET (par exemple, `web.config`).  Stockez la chaîne de connexion en utilisant l'élément `<appSettings>` :

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Utilisez l'émetteur et les valeurs de clé récupérées sur le portail de gestion, comme décrit dans la section précédente.

## Création d'une rubrique

Vous pouvez effectuer des opérations de gestion pour les rubriques et les abonnements Service Bus via la classe **NamespaceManager**. La classe **NamespaceManager** fournit des méthodes pour créer, énumérer et supprimer des files d'attente. 

Dans cet exemple, un objet **NamespaceManager** est construit à l'aide de la classe Azure **CloudConfigurationManager** avec une chaîne de connexion constituée de l'adresse de base d'un espace de noms de service Service Bus et des informations d'identification appropriées assorties d'autorisations pour les gérer. Cette chaîne de connexion se présente comme suit :

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

Voici un exemple reprenant les paramètres de configuration de la section précédente :

    // Create the topic if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Les surcharges de la méthode **CreateTopic** vous permettent de paramétrer les propriétés de la rubrique (par exemple, pour faire en sorte que la valeur par défaut de durée de vie (" time-to-live ") soit appliquée aux messages envoyés à la rubrique. Ces paramètres sont appliqués à l'aide de la classe **TopicDescription**. L'exemple suivant montre comment créer une rubrique nommée " TestTopic " avec une taille maximale de 5 Go et une durée de vie de message par défaut d'une minute.

    // Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Topic with custom settings
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**Remarque :** vous pouvez utiliser la méthode **TopicExists** sur les objets **NamespaceManager** pour vérifier s'il existe déjà une rubrique d'un certain nom dans un espace de noms de service.

<h2>Création d'abonnements</h2>

Vous pouvez également créer des abonnements de rubrique à l'aide de la classe **NamespaceManager**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est utilisé par défaut si aucun filtre n'est spécifié quand un nouvel abonnement est créé. Quand le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement " AllMessages " créé utilise le filtre par défaut **MatchAll**.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Création d'abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Parmi les types de filtre pris en charge par les abonnements, **SqlFilter**, est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus de détails sur les expressions utilisables avec un filtre SQL, examinez la syntaxe [SqlFilter.SqlExpression][].

Dans l'exemple ci-dessous, l'abonnement " HighMessages " est créé avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **MessageNumber** a une valeur supérieure à 3 :

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

De même, l'exemple suivant crée un abonnement nommé " LowMessages " avec un filtre **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **MessageNumber** inférieure ou égale à 3 :

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

Maintenant, lorsqu'un message est envoyé à " TestTopic ", il est toujours remis aux destinataires abonnés à l'abonnement à la rubrique " AllMessages " et remis de manière sélective aux destinataires abonnés aux abonnements aux rubriques " HighMessages " et " LowMessages " (en fonction du contenu du message).

## Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application crée un objet
**TopicClient** à l'aide de la chaîne de connexion.

Le code ci-dessous montre comment créer un objet **TopicClient** pour la rubrique " TestTopic " créée précédemment à l'aide de l'appel d'API **CreateFromConnectionString** :

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());
 

Les messages envoyés aux rubriques Service Bus sont des instances de la classe **BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de propriétés standard (telles que **Label** et **TimeToLive**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires. Une application peut définir le corps du message en transmettant un objet sérialisable au constructeur de **BrokeredMessage**. Le **DataContractSerializer** approprié sera alors utilisé pour sérialiser l'objet. Une autre possibilité consiste à fournir un **System.IO.Stream**.

L'exemple suivant montre comment envoyer cinq messages de test à l'expéditeur " TestTopic " **TopicClient** obtenu dans l'extrait de code précédent. Notez que la valeur de la propriété **MessageNumber** de chaque message varie au niveau de l'itération de la boucle (détermine les abonnements qui le reçoivent) :

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;

       // Send message to the topic
       Client.Send(message);
     }

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go.

## Réception des messages d'un abonnement

Le moyen recommandé de recevoir les messages d'un abonnement est d'utiliser un objet **SubscriptionClient**. Les objets **SubscriptionClient** peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : lorsque Service Bus reçoit une demande de lecture pour un message figurant dans un abonnement, il marque le message comme étant consommé et le renvoie à l'application. **Le mode ReceiveAndDelete** est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

En mode **PeekLock** (par défaut), le processus de réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant **Complete** pour le message reçu. Lorsque Service Bus obtient l'appel **Complete**, il marque le message comme étant consommé et le supprime de l'abonnement.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités à l'aide du mode par défaut **PeekLock**. Pour spécifier une autre valeur **ReceiveMode** vous pouvez utiliser une autre surcharge pour **CreateFromConnectionString**. Cet exemple utilise le rappel**OnMessage** pour traiter les messages dès leur arrivée dans l'abonnement " HighMessages ".

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    subscriptionClientHigh.OnMessage((message) =>
    {
        try
        {
            // Process message from subscription
            Console.WriteLine("\n**High Messages**");
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Message Number: " +
                message.Properties["MessageNumber"]);

            // Remove message from subscription
            message.Complete();
        }
        catch (Exception)
        {
            // Indicates a problem, unlock message in subscription
            message.Abandon();
        }
    }, options);

Cet exemple configure le rappel **OnMessage** à l'aide d'un objet **OnMessageOptions**. **AutoComplete** est défini sur **false** pour permettre  le contrôle manuel du moment d'appel de **Complete** sur le message reçu. **AutoRenewTimeout** est défini sur une minute, ce qui oblige le client à attendre jusqu'à une minute pour un message avant que l'appel expire et que le client effectue un nouvel appel pour rechercher des messages : cela réduit le nombre de fois que le client effectue des appels facturables qui ne récupèrent pas de messages.

## Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle peut appeler la méthode **Abandon** pour le message reçu (au lieu de la méthode **Complete**). Cela amène Service Bus à déverrouiller le message dans l'abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans un abonnement est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la demande **Complete**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, appelé **Au moins une fois**, chaque message est traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

## Suppression des rubriques et des abonnements

L'exemple suivant montre comment supprimer la rubrique **TestTopic** de l'espace de noms de service **HowToSample** :

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

La suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l'abonnement **HighMessages** à partir de la rubrique **TestTopic** :

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques et des abonnements Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements][].
-   Référence d'API pour [SqlFilter][].
-   Créez une application opérationnelle capable d'envoyer et de recevoir des messages à destination et en provenance d'une file d'attente Service Bus : [Didacticiel .NET sur la messagerie répartie Service Bus][].

  [Étapes suivantes]: #nextsteps
  [Présentation des rubriques et des abonnements Service Bus]: #what-is
  [Création d'un espace de noms de service]: #create-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-creds
  [Configuration de votre application pour l'utilisation de Service Bus]: #configure-app
  [ Configuration d'une chaîne de connexion Service Bus]: #set-up-connstring
  [ Configuration de votre chaîne de connexion]: #config-connstring
  [ Création d'une rubrique]: #create-topic
  [ Création d'abonnements]: #create-subscriptions
  [ Envoi de messages à une rubrique]: #send-messages
  [ Réception des messages d'un abonnement]: #receive-messages
  [ Gestion des blocages d'application et des messages illisibles]: #handle-crashes
  [ Suppression de rubriques et d'abonnements]: #delete-topics
  
  [Concepts de rubrique]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Portail de gestion Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [Files d'attente, rubriques et abonnements]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Didacticiel .NET sur la messagerie répartie Service Bus]: http://msdn.microsoft.com/library/hh367512.aspx

<!--HONumber=47-->
 