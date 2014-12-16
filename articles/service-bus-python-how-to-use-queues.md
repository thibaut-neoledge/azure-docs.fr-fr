<properties urlDisplayName="Service Bus Queues" pageTitle="Utilisation des files d'attente Service Bus (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />




# Utilisation des files d'attente Service Bus
Ce guide vous montre comment utiliser les files d'attente Service Bus. Les exemples sont
écrits dans Python et utilisent le module Python pour Azure. Les scénarios
couverts comprennent **la création de files d'attente, l'envoi et la réception de messages** et
**la suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes][].

## Sommaire

-   [Présentation des files d'attente Service Bus][]
-   [Création d'un espace de noms de service][]
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms][]
-   [ Création d'une file d'attente][]
-   [ Envoi de messages à une file d'attente][]
-   [ Réception des messages d'une file d'attente][]
-   [ Gestion des blocages d'application et des messages illisibles][]
-   [Étapes suivantes][]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Remarque :** si vous devez installer Python ou les bibliothèques clientes, consultez le [Guide d'installation de Python](../python-how-to-install/).


## <a name="create-queue"> </a>Création d'une file d'attente

L'objet **ServiceBusService** permet d'utiliser des files d'attente. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Service Bus par programme :

	from azure.servicebus import ServiceBusService, Message, Queue

Le code suivant crée un objet **ServiceBusService**. Remplacez mynamespace, sharedaccesskeyname et sharedaccesskey par un espace de noms, un nom et une valeur de clé de signature d'accès partagé réels.

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

Le nom et la valeur de la clé de signature d'accès partagé se trouvent dans les informations de connexion du portail Azure ou dans la fenêtre Propriétés de Visual Studio quand vous sélectionnez l'espace de noms Service Bus dans l'Explorateur de serveurs (comme indiqué dans la section précédente).

	bus_service.create_queue('taskqueue')

**create_queue** prend également en charge des options supplémentaires, qui
permettent de remplacer les paramètres de file d'attente par défaut comme la durée de vie de message
ou la taille de file d'attente maximale. L'exemple suivant montre comment définir la
taille de file d'attente maximale sur 5 Go et la durée de vie de message sur une minute :

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>Envoi de messages à une file d'attente

Pour envoyer un message à une file d'attente Service Bus, votre application appelle la
**méthode send\_queue\_message** sur l'objet **ServiceBusService**.

L'exemple suivant montre comment envoyer un message de test à la
file d'attente nommée *à l'aide de taskqueue* **send\_queue\_message** :

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête,
qui inclut les propriétés d'application standard et personnalisées, peut avoir
une taille maximale de 64 Ko). Il n'existe aucune limite pour le nombre de messages
dans une file d'attente, mais il en existe une, en revanche, pour la taille totale des messages
dans une file d'attente. Cette taille de file d'attente est définie lors de la création, avec une
limite maximale de 5 Go.

## <a name="receive-messages"> </a>Réception des messages d'une file d'attente

Les messages sont réceptionnés à partir d'une file d'attente à l'aide de la méthode **receive\_queue\_message**
sur l'objet **ServiceBusService** :

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

Les messages sont supprimés de la file d'attente lorsqu'ils sont lus si le paramètre
**peek\_lock** est défini sur **False**. Vous pouvez lire et verrouiller le
message sans le supprimer de la file d'attente en définissant le paramètre
**peek\_lock** sur **True**.

Le comportement de lecture et de suppression du message dans le cadre de
l'opération de réception constitue le modèle le plus simple et le mieux adapté aux scénarios pour
lesquels une application peut tolérer l'absence de traitement d'un message en cas
d'échec. Pour comprendre, imaginez un scénario dans lequel le
consommateur émet la demande de réception, puis se bloque avant le traitement
. Comme Service Bus a marqué le message comme étant consommé,
lorsque l'application redémarre et recommence à consommer des messages,
elle manque le message consommé avant l'incident.


Si le paramètre **peek\_lock** est défini sur **True**, la réception devient
une opération en deux étapes, qui autorise une prise en charge des applications
qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une
demande, il recherche le message suivant à consommer et le verrouille pour empêcher
d'autres consommateurs de le recevoir, puis le renvoie à l'application.
Dès que l'application a terminé le traitement du message (ou qu'elle l'a stocké
de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du
processus de réception en appelant la méthode **delete** pour l'objet **Message**
. La méthode **delete** marque le message comme étant consommé
et le supprime de la file d'attente.

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## <a name="handle-crashes"> </a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération
à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une
application réceptrice ne parvient pas à traiter le message pour une raison quelconque,
elle appelle la méthode **unlock** pour l'objet 
**Message**. Cela amène Service Bus à déverrouiller le
message dans la file d'attente de messages et à le rendre à nouveau disponible en réception,
pour la même application consommatrice ou pour une autre.
.

Il existe également un délai d'expiration pour un message verrouillé dans la
file d'attente et si l'application ne parvient pas à traiter le message avant l'
expiration du délai d'attente du verrou (par exemple, si l'application se bloque), Service
Bus déverrouille le message automatiquement et le rend à nouveau disponible
en réception.

Dans le cas où l'application se bloque après le traitement du message
, mais avant l'appel de la méthode **delete,** le message est 
à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement,
**souvent appelé Au moins une fois**, chaque message est traité au moins une fois.
Toutefois, dans certaines circonstances, un même message peut être
remis une nouvelle fois. Si le scénario ne peut pas tolérer un traitement en double,
les développeurs d'applications doivent ajouter une logique supplémentaire à leur application
pour traiter la remise de messages en double. Cette opération est souvent réalisée à l'aide de la
**propriété MessageId** d'un message, qui reste constante pendant
les tentatives de remise.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces
liens pour en savoir plus.

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements.][]

  [Étapes suivantes]: #next-steps
  [Présentation des files d'attente Service Bus]: #what-are-service-bus-queues
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [ Création d'une file d'attente]: #create-queue
  [ Envoi de messages à une file d'attente]: #send-messages
  [ Réception des messages d'une file d'attente]: #receive-messages
  [ Gestion des blocages d'application et des messages illisibles]: #handle-crashes
  [Concepts de la file d'attente]: ../../../DevCenter/dotNet/Media/sb-queues-08.png
  [Portail de gestion Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
