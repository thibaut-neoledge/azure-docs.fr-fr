<properties 
	pageTitle="Utilisation des rubriques Service Bus (Python) - Azure" 
	description="Découvrez comment utiliser les rubriques et abonnements Service Bus Azure depuis Python." 
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
	ms.date="02/09/2015" 
	ms.author="huvalo"/>





# Utilisation des rubriques/abonnements Service Bus
Ce guide vous montre comment utiliser les rubriques et les abonnements Service Bus. Les exemples sont écrits en Python et utilisent le [package Azure Python][]. Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements, création de filtres d'abonnement, envoi de messages** à une rubrique, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**. Pour plus d'informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes](#Next_Steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

**Remarque :** si vous devez installer Python ou le [package Azure Python][], veuillez consulter le [Guide d'installation de Python](../python-how-to-install/)


## Création d'une rubrique

L'objet **ServiceBusService** vous permet d'utiliser des rubriques. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Service Bus par programme :

	from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME

Le code suivant crée un objet **ServiceBusService**. Remplacez 'mynamespace', 'sharedaccesskeyname' et 'sharedaccesskey' par un espace de noms, un nom et une valeur de clé de signature d'accès partagé réels.

	bus_service = ServiceBusService(
		service_namespace='mynamespace',
		shared_access_key_name='sharedaccesskeyname',
		shared_access_key_value='sharedaccesskey')

Le nom et la valeur de la clé de signature d'accès partagé se trouvent dans les informations de connexion du portail Azure ou dans la fenêtre Propriétés de Visual Studio quand vous sélectionnez l'espace de noms Service Bus dans l'Explorateur de serveurs (comme indiqué dans la section précédente).

	bus_service.create_topic('mytopic')

**create\_topic** prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de rubrique par défaut comme la durée de vie du message ou la taille maximale de la rubrique. L'exemple suivant montre comment définir la taille maximale de la rubrique sur 5 Go et la durée de vie de message sur une minute :

	topic_options = Topic()
	topic_options.max_size_in_megabytes = '5120'
	topic_options.default_message_time_to_live = 'PT1M'

	bus_service.create_topic('mytopic', topic_options)

## Création d'abonnements

Les abonnements de rubrique sont également créés avec l'objet **ServiceBusService**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

**Remarque** : Les abonnements sont persistants et continuent à exister jusqu'à leur suppression ou celle de la rubrique à laquelle ils sont associés.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre n'est spécifié lors de la création d'un abonnement. Quand le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement 'AllMessages' qui est créé utilise le filtre par défaut **MatchAll**.

	bus_service.create_subscription('mytopic', 'AllMessages')

### Création d'abonnements avec des filtres

Vous pouvez également configurer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Parmi les types de filtre pris en charge par les abonnements, **SqlFilter**, est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus de détails sur les expressions utilisables avec un filtre SQL, examinez la syntaxe [SqlFilter.SqlExpression][].

Il est possible d'ajouter des filtres à un abonnement en utilisant la méthode **create\_rule** de l'objet **ServiceBusService**. Cette méthode vous permet d'ajouter de nouveaux filtres à un abonnement existant.

**Remarque** : Le filtre par défaut étant appliqué automatiquement à tous les nouveaux abonnements, vous devez d'abord le supprimer, sinon le filtre **MatchAll** remplacera tous les filtres que vous spécifiez. Vous pouvez supprimer la règle par défaut en utilisant la méthode **delete\_rule** de l'objet **ServiceBusService**.

Dans l'exemple ci-dessous, l'abonnement 'HighMessages' est créé avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **messagenumber** a une valeur supérieure à 3 :

	bus_service.create_subscription('mytopic', 'HighMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber > 3'

	bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)

De même, l'exemple suivant crée un abonnement nommé " LowMessages " avec un filtre **SqlFilter** qui sélectionne uniquement les messages qui ont une propriété **messagenumber** inférieure ou égale à 3 :

	bus_service.create_subscription('mytopic', 'LowMessages')

	rule = Rule()
	rule.filter_type = 'SqlFilter'
	rule.filter_expression = 'messagenumber <= 3'

	bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
	bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)

À présent, dès lors qu'un message est envoyé vers la rubrique 'mytopic', il est toujours remis aux destinataires abonnés à l'abonnement de rubrique 'AllMessages' et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique 'HighMessages' et'LowMessages' (en fonction du contenu du message).

## Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application doit utiliser la méthode **send\_topic\_message** de l'objet **ServiceBusService**.

L'exemple suivant montre comment envoyer cinq messages de test à 'mytopic'. Notez que la valeur de la propriété **messagenumber** de chaque message varie au niveau de l'itération de la boucle (ce qui détermine les abonnements qui le reçoivent) :

	for i in range(5):
		msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
		bus_service.send_topic_message('mytopic', msg)

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Mo (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Mo). Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go.

## Réception des messages d'un abonnement

Les messages sont reçus d'un abonnement à l'aide de la méthode **receive\_subscription\_message** sur l'objet **ServiceBusService** :

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
	print(msg.body)

Les messages sont supprimés de l'abonnement lorsqu'ils sont lus si le paramètre **peek\_lock** est défini sur **False**. Vous pouvez lire (afficher un aperçu) et verrouiller le message sans le supprimer de la file d'attente en définissant le paramètre **peek\_lock** sur **True**.

Le comportement de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.


Si le paramètre **peek\_lock** est défini sur **True**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete** sur l'objet **Message**. La méthode **delete** marque le message comme étant consommé et le supprime de l'abonnement.

	msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
	print(msg.body)

	msg.delete()


## Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne peut pas traiter le message pour une raison quelconque, elle peut appeler la méthode **unlock** sur l'objet **Message**. Cela amène Service Bus à déverrouiller le message dans l'abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans un abonnement est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible

Si l'application se bloque après le traitement du message, mais avant l'appel de la méthode **delete**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, appelé **Au moins une fois**, chaque message est traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, Cette opération est souvent réalisée à l'aide de la propriété **MessageId** qui reste constante pendant les tentatives de remise.

## Suppression des rubriques et des abonnements

Les rubriques et les abonnements sont persistants et doivent être supprimés de façon explicite par le biais du portail de gestion Azure ou par programme. L'exemple suivant montre comme supprimer la rubrique intitulée 'mytopic' :

	bus_service.delete_topic('mytopic')

La suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l'abonnement " HighMessages " de la rubrique 'mytopic' :

	bus_service.delete_subscription('mytopic', 'HighMessages')

## Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements][].
-   Référence pour [SqlFilter.SqlExpression][].

[Portail de gestion Azure]: http://manage.windowsazure.com
[Package Azure Python]: https://pypi.python.org/pypi/azure  
[Files d'attente, rubriques et abonnements]: http://msdn.microsoft.com/library/hh367516.aspx
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

<!--HONumber=47-->
