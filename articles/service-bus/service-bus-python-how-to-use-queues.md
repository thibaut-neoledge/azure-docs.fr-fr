<properties 
	pageTitle="Utilisation des files d’attente Service Bus avec Python | Microsoft Azure" 
	description="Découvrez comment utiliser les files d'attente Service Bus Azure depuis Python." 
	services="service-bus" 
	documentationCenter="python" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="sethm"/>


# Utilisation des files d’attente Service Bus

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article décrit l’utilisation des files d’attente Service Bus. Les exemples sont écrits en Python et utilisent le [package Azure Python][]. Les scénarios couverts dans ce guide sont les suivants : **création de files d'attente, envoi et réception de messages** et **suppression de files d'attente**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE]Pour installer Python ou le [package Azure Python][], consultez le [Guide d’installation de Python](../python-how-to-install.md).

## Création d’une file d’attente

L’objet **ServiceBusService** permet d’utiliser des files d’attente. Ajoutez le code suivant au début de chaque fichier Python dans lequel vous souhaitez accéder à Service Bus par programme :

```
from azure.servicebus import ServiceBusService, Message, Queue
```

Le code suivant crée un objet **ServiceBusService**. Remplacez `mynamespace`, `sharedaccesskeyname` et `sharedaccesskey` par votre espace de noms, le nom et la valeur de clé de signature d’accès partagé (SAP).

```
bus_service = ServiceBusService(
	service_namespace='mynamespace',
	shared_access_key_name='sharedaccesskeyname',
	shared_access_key_value='sharedaccesskey')
```

Le nom et la valeur de la clé de signature d’accès partagé se trouvent dans les informations de connexion du [portail Azure][] ou dans le volet **Propriétés** de Visual Studio quand vous sélectionnez l’espace de noms Service Bus dans l’Explorateur de serveurs (comme indiqué dans la section précédente).

```
bus_service.create_queue('taskqueue')
```

**create\_queue** prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de file d’attente par défaut comme la durée de vie (TTL) du message ou la taille maximale de la file d’attente. L’exemple suivant définit la taille maximale de la file d’attente sur 5 Go et la durée de vie de message sur 1 minute :

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## Envoi de messages à une file d'attente

Pour envoyer un message à une file d’attente Service Bus, votre application appelle la méthode **send\_queue\_message** sur l’objet **ServiceBusService**.

L'exemple suivant montre comment envoyer un message test à la file d'attente nommée *taskqueue* au moyen de **send\_queue\_message** :

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Files d’attente Azure et files d’attente Service Bus][].

## Réception des messages d'une file d'attente

La méthode **receive\_queue\_message** de l'objet **ServiceBusService** permet de recevoir les messages d'une file d'attente :

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Les messages sont supprimés de la file d’attente au fur et à mesure de leur lecture, si le paramètre **peek\_lock** est défini sur **False**. Vous pouvez lire (afficher un aperçu) et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre **peek\_lock** sur **True**.

Le comportement de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

Si le paramètre **peek\_lock** est défini sur **True**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete** sur l'objet **Message**. La méthode **delete** marque le message comme étant consommé et le supprime de la file d'attente.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## Gestion des blocages d’application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock** pour l'objet **Message**. Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la méthode **delete**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

## Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces liens pour en savoir plus :

-   Consultez [Files d’attente, rubriques et abonnements][].

[portail Azure]: http://manage.windowsazure.com
[package Azure Python]: https://pypi.python.org/pypi/azure
[Files d’attente, rubriques et abonnements]: service-bus-queues-topics-subscriptions.md
[Files d’attente Azure et files d’attente Service Bus]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas
 

<!---HONumber=Oct15_HO4-->