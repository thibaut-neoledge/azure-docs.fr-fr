<properties linkid="develop-python-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="" solutions="" manager="" editor="" />

Utilisation des files d'attente Service Bus
===========================================

Ce guide vous montre comment utiliser les files d'attente Service Bus. Les exemples sont écrits en Python et utilisent le module Azure Python. Les scénarios couverts dans ce guide sont les suivants : **création de files d'attente, envoi et réception de messages** et **suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

-   [Présentation des files d'attente Service Bus](#what-are-service-bus-queues)
-   [Création d'un espace de noms de service](#create-a-service-namespace)
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms](#obtain-default-credentials)
-   [Création d'une file d'attente](#create-queue)
-   [Envoi de messages à une file d'attente](#send-messages)
-   [Réception des messages d'une file d'attente](#receive-messages)
-   [Gestion des blocages d'application et des messages illisibles](#handle-crashes)
-   [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Remarque :** si vous devez installer Python ou les bibliothèques clientes, consultez le [Guide d'installation de Python](../python-how-to-install/).

Création d'une file d'attente
-----------------------------

L'objet **ServiceBusService** permet d'utiliser des files d'attente. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Service Bus par programme :

    from azure.servicebus import *

Le code suivant crée un objet **ServiceBusService**. Remplacez « mynamespace », « mykey » et « myissuer » par un espace de noms, une clé et un émetteur réels.

    bus_service = ServiceBusService(service_namespace='mynamespace', account_key='mykey', issuer='myissuer')

    bus_service.create_queue('taskqueue')

**create\_queue** prend également en charge des options supplémentaires qui vous permettent de remplacer les paramètres de file d'attente par défaut comme la durée de vie de message ou la taille de file d'attente maximale. L'exemple suivant montre comment définir la taille maximale de la file d'attente sur 5 Go et la durée de vie de message sur une minute :

    queue_options = Queue()
    queue_options.max_size_in_megabytes = '5120'
    queue_options.default_message_time_to_live = 'PT1M'

    bus_service.create_queue('taskqueue', queue_options)

Envoi de messages à une file d'attente
--------------------------------------

Pour envoyer un message à une file d'attente Service Bus, votre application appelle la méthode **send\_queue\_message** de l'objet **ServiceBusService**.

L'exemple suivant montre comment envoyer un message test à la file d'attente nommée *taskqueue* au moyen de **send\_queue\_message** :

    msg = Message('Test Message')
    bus_service.send_queue_message('taskqueue', msg)

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go.

Réception des messages d'une file d'attente
-------------------------------------------

La méthode **receive\_queue\_message** de l'objet **ServiceBusService** permet de recevoir les messages d'une file d'attente :

    msg = bus_service.receive_queue_message('taskqueue')
    print(msg.body)

Les messages sont supprimés de la file d'attente au fur et à mesure de leur lecture. Cependant, vous pouvez lire et verrouiller le message sans le supprimer de la file d'attente en définissant le paramètre facultatif **peek\_lock** sur **True**.

Le comportement par défaut de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

Si le paramètre **peek\_lock** est défini sur **True**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete** sur l'objet **Message**. La méthode **delete** marque le message comme étant consommé et le supprime de la file d'attente.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
    print(msg.body)

    msg.delete()

Gestion des blocages d'application et des messages illisibles
-------------------------------------------------------------

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock** pour l'objet **Message**. Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la méthode **delete**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

Étapes suivantes
----------------

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements.](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx)

