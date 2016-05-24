<properties 
	pageTitle="Utilisation des rubriques Service Bus avec Python | Microsoft Azure" 
	description="Découvrez comment utiliser les rubriques et abonnements Service Bus Azure depuis Python." 
	services="service-bus" 
	documentationCenter="python" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/06/2016" 
	ms.author="sethm"/>

# Utilisation des rubriques et abonnements Service Bus

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ce guide décrit l’utilisation des rubriques et des abonnements Service Bus. Les exemples sont écrits en Python et utilisent le [package Azure Python][]. Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements**, **création de filtres d'abonnement**, **envoi de messages à une rubrique**, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**. Pour plus d’informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes](#next-steps).

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Remarque :** si vous devez installer Python ou le [package Azure Python][], veuillez consulter le [Guide d’installation de Python](../python-how-to-install.md).

## Création d'une rubrique

L’objet **ServiceBusService** permet d’utiliser des rubriques. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Service Bus par programme :

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Le code suivant crée un objet **ServiceBusService**. Remplacez `mynamespace`, `sharedaccesskeyname` et `sharedaccesskey` par un espace de noms, un nom et une valeur de clé de signature d’accès partagé (SAP) réels.

```
bus_service = ServiceBusService(
	service_namespace='mynamespace',
	shared_access_key_name='sharedaccesskeyname',
	shared_access_key_value='sharedaccesskey')
```

Vous pouvez obtenir les valeurs pour la valeur et le nom de la clé SAP à partir de la fenêtre **Informations de connexion** du [portail Azure Classic][].

```
bus_service.create_topic('mytopic')
```

**create\_topic** prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de rubrique par défaut comme la durée de vie du message ou la taille maximale de la rubrique. L'exemple suivant définit la taille maximale de la rubrique sur 5 Go et la durée de vie de message (TTL) sur 1 minute :

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## Création d'abonnements

Les abonnements à des rubriques sont également créés à l’aide de l’objet **ServiceBusService**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

> [AZURE.NOTE] Les abonnements sont persistants et continuent à exister jusqu'à leur suppression ou celle de la rubrique à laquelle ils sont abonnés.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre n'est spécifié lors de la création d'un abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement « AllMessages » qui est créé utilise le filtre par défaut **MatchAll**.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### Création d'abonnements avec des filtres

Vous pouvez également définir des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Le type de filtre le plus flexible pris en charge par les abonnements est **SqlFilter**, qui implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus d’informations sur les expressions utilisables avec un filtre SQL, examinez la syntaxe [SqlFilter.SqlExpression][].

Vous pouvez ajouter des filtres à un abonnement en utilisant la méthode **create\_rule** de l’objet **ServiceBusService**. Cette méthode vous permet d'ajouter de nouveaux filtres à un abonnement existant.

> [AZURE.NOTE] Comme le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d’abord supprimer le filtre par défaut ou le filtre **MatchAll** remplacera tous les autres filtres spécifiés. Vous pouvez supprimer la règle par défaut en utilisant la méthode **delete\_rule** de l'objet **ServiceBusService**.

Dans l'exemple suivant, l'abonnement `HighMessages` est créé avec un objet **SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **messagenumber** a une valeur supérieure à 3 :

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

De même, l’exemple suivant crée l’abonnement `LowMessages` avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété **messagenumber** a une valeur inférieure ou égale à 3 :

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

À présent, dès lors qu’un message est envoyé à `mytopic`, il est toujours remis aux destinataires abonnés à l’abonnement de rubrique **AllMessages** et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique **HighMessages** et **LowMessages** (en fonction du contenu du message).

## Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application doit utiliser la méthode **send\_topic\_message** de l'objet **ServiceBusService**.

L’exemple suivant montre comment envoyer cinq messages de test à `mytopic`. Notez que la valeur de la propriété **messagenumber** de chaque message varie au niveau de l’itération de la boucle (ce qui détermine les abonnements qui le reçoivent) :

```
for i in range(5):
	msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
	bus_service.send_topic_message('mytopic', msg)
```

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Mo (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Mo). Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus][].

## Réception des messages d'un abonnement

Les messages sont reçus de l'abonnement à l'aide de la méthode **receive\_subscription\_message** sur l'objet **ServiceBusService** :

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Les messages sont supprimés de l'abonnement au fur et à mesure de leur lecture, si le paramètre **peek\_lock** est défini sur **False**. Vous pouvez lire (afficher un aperçu) et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre **peek\_lock** sur **True**.

Le comportement de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

Si le paramètre **peek\_lock** est défini sur **True**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete** sur l'objet **Message**. La méthode **delete** marque le message comme étant consommé et le supprime de l’abonnement.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## Gestion des blocages d’application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock** pour l'objet **Message**. Cela amène Service Bus à déverrouiller le message dans l'abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu’un message verrouillé dans un abonnement est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l’application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la méthode **delete**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

## Suppression de rubriques et d'abonnements

Les rubriques et les abonnements sont persistants et doivent être supprimés de façon explicite par le biais du [portail Azure Classic][] ou par programme. L’exemple suivant indique comment supprimer la rubrique nommée `mytopic` :

```
bus_service.delete_topic('mytopic')
```

La suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l’abonnement `HighMessages` de la rubrique `mytopic` :

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques Service Bus, consultez ces liens pour en savoir plus :

-   Consultez [Files d’attente, rubriques et abonnements][].
-   Référence pour [SqlFilter.SqlExpression][].

[portail Azure Classic]: http://manage.windowsazure.com
[package Azure Python]: https://pypi.python.org/pypi/azure
[Files d’attente, rubriques et abonnements]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Quotas Service Bus]: service-bus-quotas.md

<!---HONumber=AcomDC_0511_2016-->