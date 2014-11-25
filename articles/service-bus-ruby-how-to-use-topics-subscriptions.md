<properties linkid="dev-ruby-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Ruby) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic ruby" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Ruby applications." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Topics/Subscriptions" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Utilisation des rubriques/abonnements Service Bus

Ce guide vous montre comment utiliser les rubriques et les abonnements Service Bus depuis les applications Ruby. Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements, création de filtres d'abonnement, envoi de messages** à une rubrique, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**. Pour plus d'informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

-   [Présentation des rubriques et des abonnements Service Bus][Présentation des rubriques et des abonnements Service Bus]
-   [Création d'un espace de noms de service][Création d'un espace de noms de service]
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms][Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]
-   [Création d'une application Ruby][Création d'une application Ruby]
-   [Configuration de votre application pour l'utilisation de Service Bus][Configuration de votre application pour l'utilisation de Service Bus]
-   [Configuration d'une connexion Azure Service Bus][Configuration d'une connexion Azure Service Bus]
-   [Création d'une rubrique][Création d'une rubrique]
-   [Création d'abonnements][Création d'abonnements]
-   [Envoi de messages à une rubrique][Envoi de messages à une rubrique]
-   [Réception des messages d'un abonnement][Réception des messages d'un abonnement]
-   [Gestion des blocages d'application et des messages illisibles][Gestion des blocages d'application et des messages illisibles]
-   [Suppression de rubriques et d'abonnements][Suppression de rubriques et d'abonnements]
-   [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <span id="create-a-ruby-application"></span></a>Création d'une application Ruby

Créez une application Ruby. Pour obtenir des instructions, consultez le guide [Création d'une application Ruby sur Azure][Création d'une application Ruby sur Azure].

## <span id="configure-your-application-to-use-service-bus"></span></a> Configuration de votre application pour l'utilisation de Service Bus

Pour utiliser Azure Service Bus, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1.  Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2.  Tapez « gem install azure » dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

    require "azure"

## <span id="setup-a-windows-azure-service-bus-connection"></span></a>Configuration d'une connexion Azure Service Bus

Le module Azure lit les variables d'environnement **AZURE\_SERVICEBUS\_NAMESPACE** et **AZURE\_SERVICEBUS\_ACCESS\_KEY**
 pour obtenir les informations nécessaires à la connexion à votre espace de noms Azure Service Bus. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations d'espace de noms avant d'utiliser **Azure::ServiceBusService** grâce au code suivant :

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <span id="how-to-create-a-topic"></span></a>Création d'une rubrique

L'objet **Azure::ServiceBusService** permet d'utiliser des rubriques. Le code suivant crée un objet **Azure::ServiceBusService**. Pour créer une rubrique, utilisez la méthode **create\_topic()**. L'exemple suivant crée une rubrique ou imprime l'erreur s'il en existe une.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

Vous pouvez également transmettre un objet **Azure::ServiceBus::Topic** avec des options complémentaires, ce qui vous permet de remplacer les paramètres de rubrique par défaut comme la durée de vie de message ou la taille de file maximale. L'exemple suivant montre comment définir la taille maximale de la file d'attente sur 5 Go et la durée de vie de message sur une minute :

    topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## <span id="how-to-create-subscriptions"></span></a>Création d'abonnements

Les abonnements de rubrique sont également créés à l'aide de l'objet **Azure::ServiceBusService**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

**Remarque**
 Les abonnements sont persistants et continuent à exister jusqu'à leur suppression ou celle de la rubrique à laquelle ils sont associés. Si votre application contient une logique pour la création d'un abonnement, elle doit d'abord vérifier si l'abonnement existe déjà en utilisant la méthode getSubscription.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre n'est spécifié lors de la création d'un abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement « all-messages » qui est créé utilise le filtre par défaut **MatchAll**.

    subscription = azure_service_bus_service.create_subscription("test-topic", 
      "all-messages")

### <span id="how-to-create-subscriptions"></span></a>Création d'abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Parmi les types de filtres pris en charge par les abonnements, **Azure::ServiceBus::SqlFilter** est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus de détails sur les expressions utilisables avec un filtre SQL, examinez la syntaxe[SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Il est possible d'ajouter des filtres à un abonnement en utilisant la méthode **create\_rule()** de l'objet **Azure::ServiceBusService**. Cette méthode vous permet d'ajouter de nouveaux filtres à un abonnement existant.

**Remarque**
 Étant donné que le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d'abord supprimer le filtre par défaut ou le filtre **MatchAll** remplacera tous les autres filtres spécifiés. Vous pouvez supprimer la règle par défaut en utilisant la méthode **delete\_rule()** de l'objet **Azure::ServiceBusService**.

Dans les exemples ci-dessous, l'abonnement « high-messages » est créé avec un filtre **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **message\_number** a une valeur supérieure à 3 :

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

De la même manière, l'exemple suivant créé l'abonnement « low-messages » avec un filtre **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages dont la propriété **message\_number** a une valeur inférieure ou égale à 3 :

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

À présent, lorsqu'un message est envoyé à « test-topic », il est toujours remis aux destinataires abonnés à l'abonnement de rubrique « all-messages » et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique « high-messages » et « low-messages » (en fonction du contenu du message).

## <span id="how-to-send-messages-to-a-topic"></span></a> Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application doit utiliser la méthode **send\_topic\_message** de l'objet **Azure::ServiceBusService**. Les messages envoyés aux rubriques Service Bus sont des objets **Azure::ServiceBus::BrokeredMessage**. Les objets **Azure::ServiceBus::BrokeredMessage** possèdent un ensemble de propriétés standard (telles que **label** et **time\_to\_live**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données de chaîne. Une application peut définir le corps du message en transmettant une valeur de chaîne à la méthode **send\_topic\_message()** pour remplir toutes les propriétés standard requises avec les valeurs par défaut.

L'exemple suivant montre comment envoyer cinq messages de test à la rubrique « test-topic ». Notez que la valeur de la propriété **message\_number** de chaque message varie au niveau de l'itération de la boucle (détermine l'abonnement qui le reçoit) :

    5.times do |i|
      message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
        { :message_number => i })
      azure_service_bus_service.send_topic_message("test-topic", message)
    end

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Mo (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Mo). Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go.

## <span id="how-to-receive-messages-from-a-subscription"></span></a> Réception des messages d'un abonnement

La méthode **receive\_subscription\_message()** de l'objet **Azure::ServiceBusService** permet de recevoir les messages d'un abonnement : Par défaut, les messages sont lus et verrouillés sans être supprimés de l'abonnement. Il est possible de lire et de supprimer le message de l'abonnement en définissant l'option **peek\_lock** sur **false**.

Avec le comportement par défaut, la lecture et la suppression sont une opération en deux étapes, ce qui permet de prendre en charge des applications ne pouvant pas fonctionner avec des messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete\_subscription\_message()** et en fournissant le message à supprimer sous la forme d'un paramètre. La méthode **delete\_subscription\_message()** marque le message comme étant consommé et le supprime de l'abonnement.

Si le paramètre **:peek\_lock** est défini sur **false**, la lecture et la suppression des messages suivent un modèle plus simple qui fonctionne mieux pour les scénarios dans lesquels une application peut ne pas traiter un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités à l'aide de **receive\_subscription\_message()**. Dans l'exemple, un message est d'abord reçu, puis supprimé de l'abonnement « low-messages » par le biais de **:peek\_lock** défini sur **false**. Un autre message de « high-messages » est ensuite reçu, puis supprimé via **delete\_subscription\_message()** :

    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## <span id="how-to-handle-application-crashes-and-unreadable-messages"></span></a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock\_subscription\_message()** pour l'objet **Azure::ServiceBusService**. Cela amène Service Bus à déverrouiller le message dans l'abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans un abonnement est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la méthode **delete\_subscription\_message()**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **message\_id** du message, qui reste constante pendant les tentatives de remise.

## <span id="how-to-delete-topics-and-subscriptions"></span></a> Suppression des rubriques et des abonnements

Les rubriques et les abonnements sont persistants et doivent être supprimés de façon explicite par le biais du [portail de gestion Azure][portail de gestion Azure] ou par programme. L'exemple suivant montre comme supprimer la rubrique intitulée « test-topic » :

    azure_service_bus_service.delete_topic("test-topic")

La suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l'abonnement « high-messages » de la rubrique « test-topic » :

    azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## <span id="NextSteps"></span></a>Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements.][Files d'attente, rubriques et abonnements.]
-   Référence d'API pour [SqlFilter][SqlFilter]
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby][Kit de développement logiciel (SDK) Azure pour Ruby] sur GitHub.

  [Étapes suivantes]: #NextSteps
  [Présentation des rubriques et des abonnements Service Bus]: #what-are-service-bus-topics
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [Création d'une application Ruby]: #create-a-ruby-application
  [Configuration de votre application pour l'utilisation de Service Bus]: #configure-your-application-to-use-service-bus
  [Configuration d'une connexion Azure Service Bus]: #setup-a-windows-azure-service-bus-connection
  [Création d'une rubrique]: #how-to-create-a-topic
  [Création d'abonnements]: #how-to-create-subscriptions
  [Envoi de messages à une rubrique]: #how-to-send-messages-to-a-topic
  [Réception des messages d'un abonnement]: #how-to-receive-messages-from-a-subscription
  [Gestion des blocages d'application et des messages illisibles]: #how-to-handle-application-crashes-and-unreadable-messages
  [Suppression de rubriques et d'abonnements]: #how-to-delete-topics-and-subscriptions
  [Création d'une application Ruby sur Azure]: /fr-fr/develop/ruby/tutorials/web-app-with-linux-vm/
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
