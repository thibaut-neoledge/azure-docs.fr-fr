<properties linkid="dev-ruby-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Ruby) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Ruby" description="Learn how to use Service Bus queues in Azure. Code samples written in Ruby." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Queues" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Utilisation des files d'attente Service Bus

Ce guide vous montre comment utiliser les files d'attente Service Bus. Les exemples sont
écrits en Ruby et utilisent le module Azure gem. Les scénarios
couverts dans ce guide sont les suivants : **création de files d'attente, envoi et réception de messages** et
**suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes][].

## Sommaire

-   [Présentation des files d'attente Service Bus][]
-   [Création d'un espace de noms de service][]
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms][]
-   [Création d'une application Ruby][]
-   [Configuration de votre application pour l'utilisation de Service Bus][]
-   [Configuration d'une connexion Azure Service Bus][]
-   [Création d'une file d'attente][]
-   [Envoi de messages à une file d'attente][]
-   [Réception des messages d'une file d'attente][]
-   [Gestion des blocages d'application et des messages illisibles][]
-   [Étapes suivantes][]

[WACOM.INCLUDE [howto-service-bus-queues][]]

## <span id="create-a-ruby-application"></span></a>Création d'une application Ruby

Créez une application Ruby. Pour obtenir des instructions, consultez le guide [Création d'une application Ruby sur Azure][].

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

## <span id="how-to-create-a-queue"></span></a>Création d'une file d'attente

L'objet **Azure::ServiceBusService** permet d'utiliser des files d'attente. Pour créer une file d'attente, utilisez la méthode **create\_queue()**. L'exemple suivant crée une file d'attente ou imprime l'erreur s'il en existe une.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

Vous pouvez également transmettre un objet **Azure::ServiceBus::Queue** avec des options complémentaires, ce qui vous permet de remplacer les paramètres de file d'attente par défaut comme la durée de vie de message ou la taille de file maximale. L'exemple suivant montre comment définir la taille maximale de la file d'attente sur 5 Go et la durée de vie de message sur une minute :

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

## <span id="how-to-send-messages-to-a-queue"></span></a>Envoi de messages à une file d'attente

Pour envoyer un message à une file d'attente Service Bus, votre application appelle la méthode **send\_queue\_message()** de l'objet **Azure::ServiceBusService**. Les messages envoyés aux files d'attente Service Bus (et reçus de celles-ci) sont les objets **Azure::ServiceBus::BrokeredMessage**. Ils possèdent un ensemble de propriétés standard (telles que **label** et **time\_to\_live**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires. Une application peut définir le corps du message en transmettant une valeur de chaîne en tant que message pour remplir toutes les propriétés standard requises avec les valeurs par défaut.

L'exemple suivant indique comment envoyer un message test à la file d'attente nommée « test-queue » au moyen de **send\_queue\_message()** :

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go.

## <span id="how-to-receive-messages-from-a-queue"></span></a>Réception des messages d'une file d'attente

La méthode **receive\_queue\_message()** de l'objet **Azure::ServiceBusService** permet de recevoir les messages d'une file d'attente : Par défaut, les messages sont lus et verrouillés sans être supprimés de la file d'attente. Il est toutefois possible de supprimer ces messages de la file d'attente lors de leur lecture en paramétrant l'option **:peek\_lock** sur **false**.

Avec le comportement par défaut, la lecture et la suppression sont une opération en deux étapes, ce qui permet de prendre en charge des applications ne pouvant pas fonctionner avec des messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete\_queue\_message()** et en fournissant le message à supprimer sous la forme d'un paramètre. La méthode **delete\_queue\_message()** marque le message comme étant consommé et le supprime de la file d'attente.

Si le paramètre **:peek\_lock** est défini sur **false**, la lecture et la suppression des messages suivent un modèle plus simple qui fonctionne mieux pour les scénarios dans lesquels une application peut ne pas traiter un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités à l'aide de **receive\_queue\_message()**. Dans l'exemple, un message est d'abord reçu puis supprimé par le biais de **:peek\_lock** défini sur **false**. Un autre message est ensuite reçu, puis supprimé via **delete\_queue\_message()** :

    message = azure_service_bus_service.receive_queue_message("test-queue", 
      { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

## <span id="how-to-handle-application-crashes-and-unreadable-messages"></span></a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock\_queue\_message()** pour l'objet **Azure::ServiceBusService**. Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la méthode **delete\_queue\_message()**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **message\_id** du message, qui reste constante pendant les tentatives de remise.

## <span id="next-steps"></span></a>Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements.][]
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby][] sur GitHub.

Pour consulter un comparatif entre les files d'attente Azure Service Bus évoquées dans cet article et les files d'attente Azure présentées dans l'article [Utilisation du service de file d'attente Azure][], voir [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences][]

  [Étapes suivantes]: #next-steps
  [Présentation des files d'attente Service Bus]: #what-are-service-bus-queues
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [Création d'une application Ruby]: #create-a-ruby-application
  [Configuration de votre application pour l'utilisation de Service Bus]: #configure-your-application-to-use-service-bus
  [Configuration d'une connexion Azure Service Bus]: #setup-a-windows-azure-service-bus-connection
  [Création d'une file d'attente]: #how-to-create-a-queue
  [Envoi de messages à une file d'attente]: #how-to-send-messages-to-a-queue
  [Réception des messages d'une file d'attente]: #how-to-receive-messages-from-a-queue
  [Gestion des blocages d'application et des messages illisibles]: #how-to-handle-application-crashes-and-unreadable-messages
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [Création d'une application Ruby sur Azure]: /fr-fr/develop/ruby/tutorials/web-app-with-linux-vm/
  [Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
  [Kit de développement logiciel (SDK) Azure pour Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
  [Utilisation du service de file d'attente Azure]: /fr-fr/develop/ruby/how-to-guides/queue-service/
  [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh767287.aspx
