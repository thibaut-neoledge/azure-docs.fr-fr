---
title: "Utilisation des files d’attente Azure Service Bus avec Ruby | Microsoft Docs"
description: "Découvrez comment utiliser les files d&quot;attente Service Bus dans Azure. Exemples de code écrits en Ruby."
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 01/11/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0f9f732d6998a6ee50b0aea4edfc615ac61025ce
ms.openlocfilehash: 343dc0d39f284488f03e1d1ba3df21ae616e97d9


---
# <a name="how-to-use-service-bus-queues"></a>Utilisation des files d’attente Service Bus
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ce guide décrit l’utilisation des files d’attente Service Bus. Les exemples sont écrits en Ruby et utilisent le module Azure gem. Les scénarios couverts dans ce guide sont les suivants : **création de files d’attente, envoi et réception de messages** et **suppression de files d’attente**. Pour plus d’informations sur les files d’attente Service Bus, consultez la section [Étapes suivantes](#next-steps).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]
   
## <a name="create-a-ruby-application"></a>Création d'une application Ruby
Créez une application Ruby. Pour obtenir des instructions, consultez le guide [Création d’une application Ruby sur Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configuration de votre application pour l’utilisation de Service Bus
Pour utiliser Azure Service Bus, téléchargez et utilisez le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilisation de RubyGems pour obtenir le package
1. Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Tapez « gem install azure » dans la fenêtre de commande pour installer gem et les dépendances.

### <a name="import-the-package"></a>Importation du package
À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Configuration d’une connexion Service Bus Azure
Le module Azure lit les variables d’environnement **AZURE\_SERVICEBUS\_NAMESPACE** et **AZURE\_SERVICEBUS\_ACCESS_KEY** pour obtenir les informations nécessaires à la connexion à votre espace de noms Service Bus. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations d’espace de noms avant d’utiliser **Azure::ServiceBusService** grâce au code suivant :

```ruby
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Attribuez à l'espace de noms la valeur que vous avez créée plutôt que l'URL entière. Par exemple, utilisez **« votreespacedenomsexemple »** et non « votreespacedenomsexemple.servicebus.windows.net ».

## <a name="how-to-create-a-queue"></a>Création d’une file d’attente
L’objet **Azure::ServiceBusService** permet d’utiliser des files d’attente. Pour créer une file d’attente, utilisez la méthode **create_queue()**. L'exemple suivant crée une file d'attente ou imprime l'erreur le cas échéant.

```ruby
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Vous pouvez également passer un objet **Azure::ServiceBus::Queue** avec des options complémentaires, ce qui vous permet de remplacer les paramètres de file d’attente par défaut comme la durée de vie de message ou la taille de file maximale. L'exemple suivant montre comment définir la taille maximale de la file d'attente sur 5 Go et la durée de vie de message sur 1 minute :

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Envoi de messages à une file d’attente
Pour envoyer un message à une file d’attente Service Bus, votre application appelle la méthode **send\_queue\_message()** de l’objet **Azure::ServiceBusService**. Les messages envoyés aux files d’attente Service Bus (et reçus de celles-ci) sont des objets **Azure::ServiceBus::BrokeredMessage**. Ils possèdent un ensemble de propriétés standard (telles que **label** et **time\_to\_live**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu’un corps de données d’application arbitraires. Une application peut définir le corps du message en transmettant une valeur de chaîne en tant que message pour remplir toutes les propriétés standard requises avec les valeurs par défaut.

L’exemple suivant indique comment envoyer un message test à la file d’attente nommée « myqueue » au moyen de **send\_queue\_message()** :

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Les files d’attente Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et d’1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go.

## <a name="how-to-receive-messages-from-a-queue"></a>Réception des messages d’une file d’attente
La méthode **receive\_queue\_message()** de l’objet **Azure::ServiceBusService** permet de recevoir les messages d’une file d’attente. Par défaut, les messages sont lus et verrouillés sans être supprimés de la file d'attente. Il est toutefois possible de supprimer ces messages de la file d’attente lors de leur lecture en paramétrant l’option **:peek_lock** sur **false**.

Avec le comportement par défaut, la lecture et la suppression sont une opération en deux étapes, ce qui permet également de prendre en charge des applications ne pouvant pas fonctionner avec des messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete\_queue\_message()** et en fournissant le message à supprimer sous la forme d’un paramètre. La méthode **delete\_queue\_message()** marque le message comme étant consommé et le supprime de la file d’attente.

Si le paramètre **:peek\_lock** est défini sur **false**, la lecture et la suppression des messages suivent un modèle plus simple qui fonctionne mieux pour les scénarios dans lesquels une application peut ne pas traiter un message en cas d’échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l’application redémarre et recommence à consommer des messages, elle manque le message consommé avant l’incident.

L’exemple suivant montre comment recevoir et traiter des messages à l’aide de la méthode **receive\_queue\_message()**. Dans l’exemple, un message est d’abord reçu puis supprimé par le biais de **:peek\_lock** défini sur **false**. Un autre message est ensuite reçu, puis supprimé via **delete\_queue\_message()** :

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d’application et des messages illisibles
Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock\_queue\_message()** pour l’objet **Azure::ServiceBusService**. Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l’application subit un incident après le traitement du message, mais avant l’émission de la méthode **delete\_queue\_message()**, le message est à nouveau remis à l’application lorsqu’elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d’application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **message\_id** du message, qui reste constante pendant les tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces liens pour en savoir plus :

* Vue d’ensemble des [files d’attente, rubriques et abonnements](service-bus-queues-topics-subscriptions.md).
* Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby](https://github.com/Azure/azure-sdk-for-ruby) sur GitHub.

Pour consulter un comparatif entre les files d’attente Azure Service Bus évoquées dans cet article et les files d’attente Azure présentées dans l’article [Utilisation de Queue Storage à partir de Ruby](../storage/storage-ruby-how-to-use-queue-storage.md), consultez [Files d’attente Azure et files d’attente Azure Service Bus - comparaison et différences](service-bus-azure-and-service-bus-queues-compared-contrasted.md)




<!--HONumber=Jan17_HO2-->


