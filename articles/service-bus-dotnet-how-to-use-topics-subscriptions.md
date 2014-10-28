<properties linkid="dev-net-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (.NET) - Azure" metaKeywords="Get started Azure Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions C# " description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for .NET applications. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm"></tags>

# Utilisation des rubriques/abonnements Service Bus

<span>Ce guide vous montre comment utiliser les rubriques et
les abonnements Service Bus. Les exemples sont écrits en C# et utilisent l'API .NET. Les
scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements,création
de filtres d'abonnement, envoi de messages** à une rubrique, **réception
de messages en provenance d'un abonnement** et **suppression de rubriques et
d'abonnements**. Pour plus d'informations sur les rubriques et les abonnements, consultez
la section [Étapes suivantes][]. </span>

[WACOM.INCLUDE [create-account-note][]]

[WACOM.INCLUDE [howto-service-bus-topics][]]

## <span class="short-header">Configuration de l'application</span>Configuration de l'application en vue d'une utilisation de Service Bus

Lorsque vous créez une application qui fait appel à Service Bus, vous devez
ajouter une référence à l'assembly Service Bus et inclure les
espaces de noms correspondants.

## <span class="short-header">Obtention du package NuGet</span>Obtention du package NuGet Service Bus

Le package **NuGet** Service Bus est le moyen le plus simple de se procurer
l'API Service Bus et de configurer votre application avec toutes les
dépendances Service Bus. L'extension Visual Studio NuGet facilite
l'installation et la mise à jour des bibliothèques et des outils de Visual Studio et
Visual Studio Express 2012 pour le Web. Le package NuGet Service Bus est le moyen le plus simple
de se procurer l'API Service Bus et de configurer votre application avec toutes
les dépendances Service Bus.

Pour installer le package NuGet dans votre application, procédez comme suit :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **References**, puis cliquez sur
    **Manage NuGet Packages**.
2.  Recherchez « Azure » et sélectionnez l'élément **Azure
    Service Bus**. Cliquez sur **Install** pour terminer l'installation,
    puis fermez cette boîte de dialogue.

    ![][]

Vous êtes maintenant en mesure d'écrire du code pour Service Bus.

## <span class="short-header">Configuration de la chaîne de connexion</span>Configuration de la chaîne de connexion Service Bus

Service Bus utilise une chaîne de connexion pour stocker les points de terminaison et les informations d'identification. Vous pouvez placer votre chaîne de connexion dans un fichier de configuration, au lieu de la coder en dur dans le code :

-   Lorsque vous utilisez des services cloud Azure, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration de service Azure (fichiers `*.csdef` et `*.cscfg`).
-   Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé de stocker votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, un fichier `web.config`).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion en utilisant la méthode `CloudConfigurationManager.GetSetting`, tel qu'indiqué plus loin dans ce guide.

### <a name="config-connstring"> </a>Configuration de votre chaîne de connexion lors de l'utilisation de Cloud Services

Le mécanisme de configuration de service est propre aux projets
de services cloud Azure et vous permet de modifier les paramètres de configuration de façon dynamique
à partir du portail de gestion Azure sans avoir à redéployer votre
application. Par exemple, ajoutez un paramètre à votre fichier de définition de service (`*.csdef`), comme indiqué ci-dessous :

    <ServiceDefinition name="WindowsAzure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Vous devez ensuite spécifier des valeurs dans le fichier de configuration de service (`*.cscfg`) :

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

Utilisez l'émetteur et les valeurs de clé récupérées sur le portail de gestion, comme
décrit dans la section précédente.

### Configuration de votre chaîne de connexion dans le cadre de l'utilisation de Sites Web Azure ou Azure Virtual Machines

Lorsque vous utilisez Sites Web Azure ou Azure Virtual Machines, il est recommandé d'utiliser le système de configuration .NET (par exemple, `web.config`). Stockez la chaîne de connexion en utilisant l'élément `<appSettings>` :

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
        </appSettings>
    </configuration>

Utilisez l'émetteur et les valeurs de clé récupérées sur le portail de gestion, comme
décrit dans la section précédente.

## <span class="short-header">Création d'une rubrique</span>Création d'une rubrique

Vous pouvez effectuer des opérations de gestion pour les rubriques et les abonnements Service Bus via la classe **NamespaceManager**. La classe **NamespaceManager** fournit des méthodes pour créer, énumérer et supprimer des files d'attente.

Dans cet exemple, un objet **NamespaceManager** est construit à l'aide de la classe Azure **CloudConfigurationManager**
avec une chaîne de connexion constituée de l'adresse de base d'un espace de noms de service Service Bus et des informations d'identification
appropriées assorties d'autorisations pour les gérer. Cette chaîne de connexion se présente comme suit :

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedSecretIssuer=<issuerName>;SharedSecretValue=<yourDefaultKey>

Voici un exemple reprenant les paramètres de configuration de la section précédente :

    // Create the topic if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Les surcharges de la méthode **CreateTopic** vous permettent de paramétrer les propriétés
de la rubrique (par exemple, pour faire en sorte que la valeur par défaut de durée de vie (« time-to-live »)
soit appliquée aux messages envoyés à la rubrique. Ces paramètres sont appliqués
à l'aide de la classe **TopicDescription**. L'exemple suivant montre comment
créer une rubrique nommée « TestTopic » avec une taille maximale de 5 Go et une
durée de vie de message par défaut d'une minute.

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

**Remarque :** vous pouvez utiliser la méthode **TopicExists** sur les objets **NamespaceManager**
pour vérifier s'il existe déjà une rubrique d'un certain nom dans
un espace de noms de service.

## <span class="short-header">Création d'abonnements</span>Création d'abonnements

Vous pouvez également créer des abonnements de rubrique à l'aide de la classe **NamespaceManager**
. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif
qui limite l'ensemble des messages transmis à la
file d'attente virtuelle de l'abonnement.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre
n'est spécifié lors de la création d'un abonnement. Lorsque le filtre **MatchAll**
est utilisé, tous les messages publiés dans la rubrique sont placés dans la
file d'attente virtuelle de l'abonnement. Dans l'exemple suivant,
l'abonnement « AllMessages » qui est créé utilise le filtre
par défaut **MatchAll**.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Création d'abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés
à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Parmi les types de filtre pris en charge par les abonnements,
**SqlFilter** est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent
au niveau des propriétés des messages publiés dans la rubrique. Pour
plus de détails sur les expressions utilisables avec un filtre SQL,
examinez la syntaxe [SqlFilter.SqlExpression][].

Dans l'exemple ci-dessous, l'abonnement « HighMessages » est créé avec un filtre
**SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée
**messagenumber** a une valeur supérieure à 3 :

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

De la même manière, l'exemple suivant crée l'abonnement
« LowMessages » avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont
la propriété **MessageNumber** a une valeur inférieure ou égale à 3 :

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

À présent, dès lors qu'un message est envoyé à « TestTopic », il est toujours
remis aux destinataires abonnés à l'abonnement
de rubrique « AllMessages » et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique
« HighMessages » et « LowMessages » (en fonction
du contenu du message).

## <span class="short-header">Envoi de messages à une rubrique</span>Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application crée un objet
**TopicClient** en utilisant la chaîne de connexion.

L'exemple suivant montre comment créer un objet **TopicClient**
pour la rubrique « TestTopic » créée ci-dessus en utilisant l’appel d’API **CreateFromConnectionString** :

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());

Les messages envoyés aux rubriques Service Bus sont des instances de la classe
**BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de
propriétés standard (telles que **Label** et **TimeToLive**), un dictionnaire
servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps
de données d'application arbitraires. Une application peut définir le corps du
message en transmettant un objet sérialisable au constructeur de l'objet
**BrokeredMessage** ; le sérialiseur **DataContractSerializer** approprié est
alors utilisé pour sérialiser l'objet. Une autre possibilité consiste à fournir un
**System.IO.Stream**.

L'exemple suivant montre comment envoyer cinq messages de test au client
**TopicClient** « TestTopic » obtenu dans l'extrait de code précédent.
Notez la variation de la valeur de la propriété **MessageNumber** de chaque message au niveau de l'itération
de la boucle (ce qui détermine les abonnements qui
le reçoivent) :

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

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête,
qui comprend les propriétés d'application standard et personnalisées, peut avoir
une taille maximale de 64 Ko). Si une rubrique n'est pas limitée par le nombre de messages
qu'elle peut contenir, elle l'est en revanche par la taille totale des messages
qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La
limite maximale est de 5 Go.

## <span class="short-header">Réception des messages d'un abonnement</span>Réception des messages d'un abonnement

Le moyen le plus simple de recevoir les messages d'un abonnement est d'utiliser un objet
**SubscriptionClient**. Les objets **SubscriptionClient** peuvent fonctionner dans deux
modes différents : **ReceiveAndDelete** et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique :
lorsque Service Bus reçoit une demande de lecture pour un
message figurant dans un abonnement, il marque le message comme étant consommé et
le renvoie à l'application. Le mode **ReceiveAndDelete** est le modèle
le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer
le non-traitement d'un message en cas d'échec. Pour mieux comprendre,
imaginez un scénario dans lequel le consommateur émet la demande de réception
et subit un incident avant de la traiter. Comme Service Bus a marqué le message
comme consommé, lorsque l'application redémarre et
recommence à consommer des messages, elle manque le message
consommé avant l'incident.

En mode **PeekLock** (par défaut), le processus de réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui
ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande,
il recherche le prochain message à consommer, le verrouille pour empêcher d'autres
consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que
l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable
pour un traitement ultérieur), elle accomplit la deuxième étape du processus
de réception en appelant **Complete** pour le message reçu. Lorsque Service
Bus obtient l'appel **Complete**, il marque le message comme étant
consommé et le supprime de l'abonnement.

L'exemple ci-dessous montre comment les messages peuvent être reçus et
traités à l'aide du mode par défaut **PeekLock**. Pour spécifier une valeur différente pour **ReceiveMode**, vous pouvez utiliser une autre surcharge pour **CreateFromConnectionString**. Dans cet exemple, une boucle infinie est créée et les messages sont traités à mesure qu'ils parviennent à l'abonnement « HighMessages » : Notez que le chemin d'accès à l'abonnement
« HighMessages » est indiqué sous la forme « \<*chemin d'accès
à la rubrique*\>/subscriptions/\<*nom de l'abonnement*\> ».

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    Client.Receive();
     
    // Continuously process messages received from the HighMessages subscription 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("MessageNumber: " + 
                message.Properties["MessageNumber"]);

             // Remove message from subscription
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in subscription
             message.Abandon();
          }
       }
    } 

## <span class="short-header">Incidents d'application et messages illisibles</span>Traitement des incidents d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération
à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une
application réceptrice ne parvient pas à traiter le message pour une raison quelconque,
elle peut appeler la méthode **Abandon** pour le message reçu (au lieu
de la méthode **Complete**). Cela amène Service Bus à déverrouiller le
message dans l'abonnement et à le rendre à nouveau disponible en réception,
pour la même application consommatrice ou pour
une autre.

De même, il faut savoir qu'un message verrouillé dans un
abonnement est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message
dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message,
mais avant l'émission de la demande **Complete**, le message est
à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé
**Au moins une fois**, chaque message est traité au
moins une fois. Toutefois, dans certaines circonstances, un même message peut être
remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double,
les développeurs d'application doivent ajouter une logique supplémentaire à leur application
pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété
**MessageId** du message, qui reste constante pendant
les tentatives de remise.

## <span class="short-header">Suppression des rubriques et des abonnements</span>Suppression des rubriques et des abonnements

L'exemple suivant montre comment supprimer la rubrique
**TestTopic** de l'espace de noms de service
**HowToSample** :

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

La suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits
au niveau de la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le
code suivant montre comment supprimer l'abonnement
**HighMessages** de la rubrique **TestTopic** :

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## <span class="short-header">Étapes suivantes</span>Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques et des abonnements Service Bus, consultez ces
liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements][].
-   Référence d'API pour [SqlFilter][].
-   Créez une application opérationnelle, capable d'envoyer et de recevoir des messages à destination et
    en provenance d'une file d'attente Service Bus : [Didacticiel .NET sur la messagerie répartie Service Bus][].

  [Étapes suivantes]: #nextsteps
  [create-account-note]: ../includes/create-account-note.md
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  []: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  [Files d'attente, rubriques et abonnements]: http://msdn.microsoft.com/fr-fr/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/fr-fr/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Didacticiel .NET sur la messagerie répartie Service Bus]: http://msdn.microsoft.com/fr-fr/library/hh367512.aspx
