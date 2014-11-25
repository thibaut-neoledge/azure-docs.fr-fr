<properties linkid="develop-python-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Utilisation des files d'attente Service Bus

Ce guide vous montre comment utiliser les files d'attente Service Bus. Les exemples sont
écrits en Python et utilisent le module Azure Python. Les scénarios
couverts dans ce guide sont les suivants : **création de files d'attente, envoi et réception de messages** et
**suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

-   [Présentation des files d'attente Service Bus][Présentation des files d'attente Service Bus]
-   [Création d'un espace de noms de service][Création d'un espace de noms de service]
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms][Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]
-   [Création d'une file d'attente][Création d'une file d'attente]
-   [Envoi de messages à une file d'attente][Envoi de messages à une file d'attente]
-   [Réception des messages d'une file d'attente][Réception des messages d'une file d'attente]
-   [Gestion des blocages d'application et des messages illisibles][Gestion des blocages d'application et des messages illisibles]
-   [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Remarque :** si vous devez installer Python ou les bibliothèques clientes, consultez le [Guide d'installation de Python][Guide d'installation de Python].

## <a name="create-queue"> </a>Création d'une file d'attente

L'objet **ServiceBusService** permet d'utiliser des files d'attente. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Service Bus par programme :

    from azure.servicebus import *

Le code suivant crée un objet **ServiceBusService**. Remplacez « mynamespace », « sharedaccesskeyname » et « sharedaccesskey » par un espace de noms, ainsi qu'un nom et une valeur de clé de signature d'accès partagé réels.

    bus_service = ServiceBusService(
        service_namespace='mynamespace',
        shared_access_key_name='sharedaccesskeyname',
        shared_access_key_value='sharedaccesskey')

Le nom et la valeur de la clé de signature d'accès partagé se trouvent dans les informations de connexion du portail Azure ou dans la fenêtre Propriétés de Visual Studio quand vous sélectionnez l'espace de noms Service Bus dans l'Explorateur de serveurs (comme indiqué dans la section précédente).

    bus_service.create_queue('taskqueue')

**create\_queue** prend également en charge des options supplémentaires, qui
vous permettent de remplacer les paramètres de file d'attente par défaut comme la durée
de vie du message ou la taille maximale de la file d'attente. L'exemple suivant montre comment définir la taille
maximale de la file d'attente sur 5 Go et la durée de vie du message sur une minute :

    queue_options = Queue()
    queue_options.max_size_in_megabytes = '5120'
    queue_options.default_message_time_to_live = 'PT1M'

    bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Envoi de messages à une file d'attente

Pour envoyer un message à une file d'attente Service Bus, votre application appelle la méthode
**send\_queue\_message** de l'objet **ServiceBusService**.

L'exemple suivant montre comment envoyer un message de test à la
file d'attente nommée *taskqueue* à l'aide de **send\_queue\_message** :

    msg = Message('Test Message')
    bus_service.send_queue_message('taskqueue', msg)

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête,
qui comprend les propriétés d'application standard et personnalisées, peut avoir
une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre
de messages qu'elle peut contenir, elle l'est en revanche par la taille totale
des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite
maximale est de 5 Go.

## <a name="receive-messages"> </a>Réception des messages d'une file d'attente

La méthode **receive\_queue\_message** de l'objet **ServiceBusService**
 permet de recevoir les messages d'une file d'attente :

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
    print(msg.body)

Les messages sont supprimés de la file d'attente au fur et à mesure de leur lecture, si le paramètre
**peer\_lock** est défini sur **False**. Vous pouvez lire (afficher un aperçu) et verrouiller le
message sans le supprimer de la file d'attente en définissant le paramètre
**peek\_lock** sur **True**.

Le comportement de lecture et de suppression du message dans le cadre
de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans
lesquels une application est capable de tolérer le non-traitement d'un message en
cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le
consommateur envoie la demande de réception et se bloque avant de la
traiter. Service Bus ayant marqué le message comme étant consommé,
lorsque l'application redémarre et recommence à consommer des messages,
elle manque le message consommé avant le blocage.

Si le paramètre **peek\_lock** est défini sur **True**, la réception devient
une opération en deux étapes, qui autorise une prise en charge des applications
qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une
demande, il recherche le prochain message à consommer, le verrouille pour empêcher
d'autres consommateurs de le recevoir, puis le renvoie à l'application.
Une fois que l'application a terminé le traitement du message (ou qu'elle l'a
stocké de manière fiable pour le traiter plus tard), elle effectue la deuxième étape du
processus de réception en appelant la méthode **delete** sur l'objet **Message**.
 La méthode **delete** marque le message comme étant consommé
et le supprime de la file d'attente.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
    print(msg.body)

    msg.delete()

## <a name="handle-crashes"> </a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération
à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une
application réceptrice ne parvient pas à traiter le message pour une raison quelconque,
elle appelle la méthode **unlock** sur l'objet
**Message**. Service Bus déverrouille alors le message
dans la file d'attente et le rend à nouveau disponible en réception,
pour la même application consommatrice ou une autre.

Un message verrouillé dans une file d'attente est assorti d'un délai d'expiration
et si l'application ne parvient pas à traiter le message dans le
temps imparti (par exemple, si l'application se bloque), Service Bus
déverrouille le message automatiquement et le rend à nouveau disponible
en réception.

Si l'application se bloque après le traitement du message,
mais avant l'appel de la méthode **delete**, le message est à
nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé
**Au moins une fois**, chaque message est traité au
moins une fois, mais, dans certains cas, un même message
peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double,
les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour
gérer la remise de messages en double. Pour ce faire, il suffit souvent d'utiliser la propriété
**MessageId** du message, qui reste constante pendant les
tentatives de remise.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez les
liens suivants pour en savoir plus.

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements.][Files d'attente, rubriques et abonnements.]

  [Étapes suivantes]: #next-steps
  [Présentation des files d'attente Service Bus]: #what-are-service-bus-queues
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [Création d'une file d'attente]: #create-queue
  [Envoi de messages à une file d'attente]: #send-messages
  [Réception des messages d'une file d'attente]: #receive-messages
  [Gestion des blocages d'application et des messages illisibles]: #handle-crashes
  [Guide d'installation de Python]: ../python-how-to-install/
  [Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
