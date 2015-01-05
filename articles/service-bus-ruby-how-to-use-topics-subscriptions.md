<properties urlDisplayName="Service Bus Topics" pageTitle="Utilisation des rubriques Service Bus (Ruby) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic ruby" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Ruby applications." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Topics/Subscriptions" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/25/2014" ms.author="tomfitz" />





# Utilisation des rubriques/abonnements Service Bus

Ce guide vous montre comment utiliser les rubriques et les abonnements Service Bus depuis les applications Ruby. Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements, création de filtres d'abonnement, envoi de messages** à une rubrique, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**. Pour plus d'informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes](#NextSteps) .

## Sommaire
* [Présentation des rubriques et des abonnements Service Bus](#what-are-service-bus-topics)
* [Création d'un espace de noms de service](#create-a-service-namespace)
* [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms](#obtain-default-credentials)
* [Création d'une application Ruby](#create-a-ruby-application)
* [Configuration de votre application pour l'utilisation de Service Bus](#configure-your-application-to-use-service-bus)
* [Configuration d'une connexion Azure Service Bus](#setup-a-windows-azure-service-bus-connection)
* [Création d'une rubrique](#how-to-create-a-topic)
* [Création d'abonnements](#how-to-create-subscriptions)
* [Envoi de messages à une rubrique](#how-to-send-messages-to-a-topic)
* [Réception des messages d'un abonnement](#how-to-receive-messages-from-a-subscription)
* [Gestion des blocages d'application et des messages illisibles](#how-to-handle-application-crashes-and-unreadable-messages)
* [Suppression de rubriques et d'abonnements](#how-to-delete-topics-and-subscriptions)
* [Étapes suivantes](#NextSteps)

## <a name="what-are-service-bus-topics"></a>Présentation des rubriques et des abonnements Service Bus

Les rubriques et les abonnements Service Bus prennent en charge un modèle de **communication de messagerie de publication et d'abonnement**. Lors de l'utilisation de rubriques et d'abonnements, les composants d'une application distribuée ne communiquent pas directement entre eux ; ils échangent plutôt des messages via une rubrique, qui fait office d'intermédiaire.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Contrairement aux files d'attente Service Bus, où chaque message est traité par un seul consommateur, les rubriques et les abonnements fournissent une forme de communication **un-à-plusieurs** utilisant un modèle de publication et d'abonnement. Il est possible d'inscrire plusieurs abonnements à une rubrique. Lorsqu'un message est envoyé à une rubrique, il est alors mis à disposition de chaque abonnement pour être géré ou traité indépendamment.

Un abonnement à une rubrique ressemble à une file d'attente virtuelle qui reçoit des copies des messages envoyés à la rubrique. Vous pouvez éventuellement inscrire des règles de filtre pour une rubrique par abonnement, ce qui vous permet de filtrer et de restreindre les messages d'une rubrique reçus en fonction des abonnements à une rubrique.

Les rubriques et les abonnements Service Bus vous permettent de mettre votre infrastructure à l'échelle pour traiter de très nombreux messages parmi un très grand nombre d'utilisateurs et d'applications.

## <a id="create-a-service-namespace"</a>Create a Service Namespace

To begin using Service Bus queues in Azure, you must first create a service namespace. A service namespace provides a scoping container for addressing Service Bus resources within 
your application. You must create the namespace through the command-line interface because the Portal does not create the service bus with an ACS connection.

To create a namespace:

1. Open an Azure Powershell console.

2. Type the command to create an Azure service bus namespace as shown below. Provide your own namespace value and specify the same region as your application. 

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -CreateACSNamespace $true

      ![Create Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a id="obtain-default-credentials"></a>Obtention d'informations d'identification de gestion par défaut pour l'espace de noms

Afin d'effectuer des opérations de gestion, comme la création d'une file d'attente, sur le nouvel espace de noms, vous devez obtenir les informations de gestion associées.

1. Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).

2. Sélectionnez l'espace de noms Service Bus que vous avez créé.

     ![Select namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/selectns.png)

3. Au bas de la page, sélectionnez **Informations de connexion**.

      ![Select connection](./media/service-bus-ruby-how-to-use-topics-subscriptions/selectconnection.png)

4. Copiez la clé par défaut. C'est la valeur que vous utiliserez dans votre code.

       ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

## <a id="create-a-ruby-application"></a>Création d'une application Ruby

Créez une application Ruby. Pour obtenir des instructions, consultez la page [Création d'une application Ruby sur Azure](/fr-fr/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-use-service-bus"></a>Configuration de votre application pour l'utilisation de Service Bus

Pour utiliser Azure Service Bus, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1. Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Tapez " gem install azure " dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

    require "azure"

## <a id="setup-a-windows-azure-service-bus-connection"></a>Configuration d'une connexion Azure Service Bus

Le module Azure lit les variables d'environnement **AZURE\_SERVICEBUS\_NAMESPACE** et **AZURE\_SERVICEBUS\_ACCESS\_KEY** 
pour obtenir les informations nécessaires pour se connecter à votre espace de noms Azure Service Bus. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations d'espace de noms avant d'utiliser **Azure::ServiceBusService** à l'aide du code suivant :

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

Attribuez à l'espace de noms Service Bus la valeur que vous avez créée plutôt que l'URL entière. Par exemple, utilisez **" votreespacedenomsexemple "** et non " votreespacedenomsexemple.servicebus.windows.net ". 

## <a id="how-to-create-a-topic"></a>Création d'une rubrique

L'objet **Azure::ServiceBusService** permet d'utiliser des rubriques. Le code suivant crée un objet **Azure::ServiceBusService**. Pour créer une rubrique, utilisez la méthode **create\_topic()**. L'exemple suivant crée une rubrique ou imprime l'erreur s'il en existe une.

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

Vous pouvez aussi transmettre un objet **Azure::ServiceBus::Topic** avec des options complémentaires, ce qui vous permet de remplacer les paramètres de rubrique par défaut, tels que la durée de vie de message ou la taille de file d'attente maximale. L'exemple suivant montre comment définir la taille maximale de la file d'attente sur 5 Go et la durée de vie de message sur une minute :

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## <a id="how-to-create-subscriptions"></a>Création d'abonnements

Les abonnements de rubrique sont aussi créés avec l'objet **Azure::ServiceBusService**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

**Remarque**
Les abonnements sont persistants et continuent à exister jusqu'à leur suppression ou celle de la rubrique à laquelle ils sont associés. Si votre application contient une logique pour la création d'un abonnement, elle doit d'abord vérifier si l'abonnement existe déjà en utilisant la méthode getSubscription.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

**MatchAll** est le filtre utilisé par défaut si aucun filtre n'est spécifié lors de la création d'un abonnement. Quand le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement " all-messages " qui est créé utilise le filtre par défaut **MatchAll**.

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>Création d'abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Parmi les types de filtre pris en charge par les abonnements, **Azure::ServiceBus::SqlFilter** est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus de détails sur les expressions utilisables avec un filtre SQL, examinez la syntaxe [SqlFilter.SqlExpression](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) .

Il est possible d'ajouter des filtres à un abonnement en utilisant la méthode **create\_rule()** de l'objet **Azure::ServiceBusService**. Cette méthode vous permet d'ajouter de nouveaux filtres à un abonnement existant.

**Remarque** :
étant donné que le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d'abord supprimer le filtre par défaut, sinon le filtre **MatchAll** remplacera les autres filtres éventuellement spécifiés. Vous pouvez supprimer la règle par défaut en utilisant la méthode **delete\_rule()** de l'objet **Azure::ServiceBusService**.

Dans les exemples ci-dessous, l'abonnement " high-messages " est créé avec un filtre **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **message\_number** a une valeur supérieure à 3 :

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "high-messages")
	azure_service_bus_service.delete_rule("test-topic", "high-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("high-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "high-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number > 3" })
	rule = azure_service_bus_service.create_rule(rule)

De la même manière, l'exemple suivant créé l'abonnement " low-messages " avec un filtre **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages dont la propriété **message_number**  a une valeur inférieure ou égale à 3 :

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "low-messages")
	azure_service_bus_service.delete_rule("test-topic", "low-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("low-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "low-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number <= 3" })
	rule = azure_service_bus_service.create_rule(rule)

À présent, lorsqu'un message est envoyé à " test-topic ", il est toujours remis aux destinataires abonnés à l'abonnement de rubrique " all-messages " et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique " high-messages " et " low-messages " (en fonction du contenu du message).

## <a id="how-to-send-messages-to-a-topic"></a>Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application doit utiliser la méthode **send\_topic\_message()** de l'objet **Azure::ServiceBusService**. Les messages envoyés aux rubriques Service Bus sont des objets **Azure::ServiceBus::BrokeredMessage**. Les objets **Azure::ServiceBus::BrokeredMessage** possèdent un ensemble de propriétés standard (telles que **label** et **time\_to\_live**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires. Une application peut définir le corps du message en transmettant une valeur de chaîne à la méthode **send\_topic\_message()** pour renseigner toutes les propriétés standard requises avec les valeurs par défaut.

L'exemple suivant montre comment envoyer cinq messages de test à la rubrique " test-topic ". Notez que la valeur de la propriété personnalisée **message_number** de chaque message varie au niveau de l'itération de la boucle (détermine l'abonnement qui le reçoit) :

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Mo (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Mo). Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go.

## <a id="how-to-receive-messages-from-a-subscription"></a>Réception des messages d'un abonnement

La méthode **receive\_subscription\_message()** de l'objet **Azure::ServiceBusService** permet de recevoir les messages d'un abonnement. Par défaut, les messages sont lus et verrouillés sans être supprimés de l'abonnement. Il est possible de lire et de supprimer le message de l'abonnement en définissant l'option **peek\_lock** sur **false**.

Avec le comportement par défaut, la lecture et la suppression sont une opération en deux étapes, ce qui permet de prendre en charge des applications ne pouvant pas fonctionner avec des messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete\_subscription\_message()** et en fournissant le message à supprimer sous la forme d'un paramètre. La méthode **delete\_subscription\_message()** marque le message comme étant consommé et le supprime de l'abonnement.

Si le paramètre **:peek\_lock** a la valeur **false**, la lecture et la suppression des messages suivent un modèle plus simple qui fonctionne mieux pour les scénarios dans lesquels une application peut ne pas traiter un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités à l'aide de **receive\_subscription\_message()**. Dans l'exemple, un message est d'abord reçu, puis supprimé de l'abonnement " low-messages " par le biais du paramètre **:peek\_lock** défini sur **false**. Un autre message de " high-messages " est ensuite reçu, puis supprimé via **delete\_subscription\_message()** :

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## <a id="how-to-handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock\_subscription\_message()** pour l'objet **Azure::ServiceBusService**. Cela amène Service Bus à déverrouiller le message dans l'abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans un abonnement est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'appel de la méthode **delete\_subscription\_message()**, le message est à nouveau remis à l'application au moment où elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois, mais, dans certains cas, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, Pour ce faire, il suffit souvent d'utiliser la propriété **message\_id** du message, qui reste constante pendant les tentatives de remise.

## <a id="how-to-delete-topics-and-subscriptions"></a>Suppression des rubriques et des abonnements

Les rubriques et les abonnements sont persistants et doivent être supprimés de façon explicite via le [portail de gestion Azure](https://manage.windowsazure.com) ou par programmation. L'exemple suivant montre comme supprimer la rubrique intitulée " test-topic " :

	azure_service_bus_service.delete_topic("test-topic")

La suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l'abonnement " high-messages " de la rubrique " test-topic " :

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## <a id="NextSteps"></a>Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx)
-   Référence d'API pour [SqlFilter](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx)
-	Consultez le référentiel [Kit de développement logiciel (SDK) Azure pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub

<!--HONumber=35.1-->
