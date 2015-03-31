<properties 
	pageTitle="Utilisation des files d'attente Service Bus (Python) - Azure" 
	description="Découvrez comment utiliser les files d'attente Service Bus Azure depuis Python." 
	services="service-bus" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="huvalo"/>




# Utilisation des files d'attente Service Bus

Ce guide décrit l'utilisation des files d'attente Service Bus. Les exemples sont écrits en Python et utilisent le [package Azure Python][]. Les scénarios couverts dans ce guide sont les suivants : **création de files d'attente, envoi et réception de messages** et **suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes][].

[AZURE.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**Remarque :** si vous devez installer Python ou le [package Azure Python][], veuillez consulter le [Guide d'installation de Python](../python-how-to-install/)


## Création d'une file d'attente

L'objet **ServiceBusService** permet d'utiliser des files d'attente. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Service Bus par programme :

	from azure.servicebus import ServiceBusService, Message, Queue

Le code suivant crée un objet **ServiceBusService**. Remplacez 'mynamespace', 'sharedaccesskeyname' et 'sharedaccesskey' par un espace de noms, un nom et une valeur de clé de signature d'accès partagé (SAP) réels.

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

Le nom et la valeur de la clé de signature d'accès partagé se trouvent dans les informations de connexion du portail Azure ou dans la fenêtre Propriétés de Visual Studio quand vous sélectionnez l'espace de noms Service Bus dans l'Explorateur de serveurs (comme indiqué dans la section précédente).

	bus_service.create_queue('taskqueue')

**create_queue** prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de file d'attente par défaut comme la durée de vie du message ou la taille maximale de la file d'attente. L'exemple suivant montre comment définir la taille maximale de la file d'attente sur 5 Go et la durée de vie de message sur une minute :

	queue_options = Queue()
	queue_options.max_size_in_megabytes = '5120'
	queue_options.default_message_time_to_live = 'PT1M'

	bus_service.create_queue('taskqueue', queue_options)

## Envoi de messages à une file d'attente

Pour envoyer un message à une file d'attente Service Bus, votre application appelle la méthode **send\_queue\_message** de l'objet **ServiceBusService**.

L'exemple suivant montre comment envoyer un message test à la file d'attente nommée *taskqueue using* à l'aide de la méthode **send\_queue\_message()** :

	msg = Message(b'Test Message')
	bus_service.send_queue_message('taskqueue', msg)

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go.

## Réception des messages d'une file d'attente

La méthode **receive\_queue\_message** de l'objet **ServiceBusService** permet de recevoir les messages d'une file d'attente :

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
	print(msg.body)

Les messages sont supprimés de la file d'attente au fur et à mesure de leur lecture, si le paramètre **peek\_lock** est défini sur **False**. Vous pouvez lire (afficher un aperçu) et verrouiller le message sans le supprimer de la file d'attente en définissant le paramètre **peek\_lock** sur **True**.

Le comportement de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.


Si le paramètre **peek\_lock** est défini sur **True**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete** sur l'objet **Message**. La méthode **delete** marque le message comme étant consommé et le supprime de la file d'attente.

	msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
	print(msg.body)

	msg.delete()

## Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock** pour l'objet **Message**. Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application se bloque après le traitement du message, mais avant l'appel de la méthode **delete**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, appelé **Au moins une fois**, chaque message est traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

## Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements][].

[Portail de gestion Azure]: http://manage.windowsazure.com
[Package Azure Python]: https://pypi.python.org/pypi/azure  
[Files d'attente, rubriques et abonnements]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx

<!--HONumber=47-->
