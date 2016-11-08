---
title: Utilisation des files d’attente Service Bus avec Java | Microsoft Docs
description: Découvrez comment utiliser les files d'attente Service Bus dans Azure. Exemples de code écrits en Java.
services: service-bus
documentationcenter: java
author: sethmanheim
manager: timlt

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: sethm

---
# <a name="how-to-use-service-bus-queues"></a>Utilisation des files d’attente Service Bus
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article décrit l’utilisation des files d’attente Service Bus. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel (SDK) Azure pour Java][Kit de développement logiciel (SDK) Azure pour Java]. Les scénarios couverts dans ce guide sont les suivants : **création de files d’attente**, **envoi et réception de messages** et **suppression de files d’attente**.

## <a name="what-are-service-bus-queues?"></a>Présentation des files d'attente Service Bus
Les files d’attente Service Bus prennent en charge un modèle de communication de **messagerie répartie** . Lors de l’utilisation de files d’attente, les composants d’une application distribuée ne communiquent pas directement entre eux ; ils échangent plutôt des messages via une file d’attente, qui fait office d’intermédiaire (broker). Un producteur de messages (expéditeur) remet un message à la file d’attente, puis continue son traitement.
De manière asynchrone, un consommateur de message (destinataire) extrait le message de la file d’attente, puis le traite. L’expéditeur ne doit pas forcément attendre une réponse du destinataire afin de continuer à traiter et à envoyer d’autres messages. Les files d’attente permettent la livraison de messages sur le principe du **premier entré, premier sorti (FIFO)** à un ou plusieurs destinataires concurrents. Autrement dit, les messages sont en général reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente ; chaque message est reçu et traité par un seul consommateur de message uniquement.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Les files d’attente Service Bus sont une technologie à usage généraliste pouvant servir à une grande diversité de situations :

* Communication entre les rôles Web et les rôles de travail dans une application multiniveau Azure.
* Communication entre les applications locales et les applications hébergées par Azure dans une solution hybride.
* Communication entre les composants d’une application distribuée s’exécutant en local dans différentes organisations ou dans différents services d’une organisation.

L’utilisation des files d’attente permet une meilleure montée en charge de vos applications et une plus grande résilience dans votre architecture.

## <a name="create-a-service-namespace"></a>Création d'un espace de noms de service
Pour utiliser les files d’attente Service Bus dans Azure, vous devez d’abord créer un espace de noms. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms :

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configuration de votre application pour l’utilisation de Service Bus
Vérifiez que vous avez installé le [Kit de développement logiciel (SDK) Azure pour Java][Kit de développement logiciel (SDK) Azure pour Java] avant de créer cet exemple. Si vous utilisez Eclipse, vous pouvez installer le [Kit de ressources Azure pour Eclipse][Kit de ressources Azure pour Eclipse] qui inclut le Kit de développement logiciel (SDK) Azure pour Java. Vous pouvez ensuite ajouter les **bibliothèques Microsoft Azure pour Java** à votre projet :

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Ajoutez les instructions `import` suivantes au début du fichier Java :

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Création d’une file d’attente
Vous pouvez effectuer des opérations de gestion pour les files d’attente Service Bus via la classe **ServiceBusContract**. Un objet **ServiceBusContract** est construit avec une configuration appropriée qui encapsule le jeton SAP avec des autorisations pour le gérer, et la classe **ServiceBusContract** est le point de communication unique avec Azure.

La classe **ServiceBusService** fournit des méthodes pour créer, énumérer et supprimer des files d’attente. L’exemple suivant montre comment un objet **ServiceBusService** peut servir à créer une file d’attente nommée « TestQueue » avec un espace de noms nommé « HowToSample » :

```
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Il existe des méthodes sur **QueueInfo** qui permettent de paramétrer des propriétés de la file d’attente, par exemple : la valeur par défaut « time-to-live » (TTL) à appliquer aux messages envoyés à la file d’attente. L’exemple suivant montre comment créer une file d’attente nommée `TestQueue` avec une taille maximale de 5 Go :

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Notez que vous pouvez utiliser la méthode **listQueues** sur les objets **ServiceBusContract** pour vérifier s’il existe déjà une file d’attente d’un nom déterminé dans un espace de noms de service.

## <a name="send-messages-to-a-queue"></a>Envoi de messages à une file d'attente
Pour envoyer un message à une file d’attente Service Bus, votre application obtient un objet **ServiceBusContract**. Le code suivant montre comment envoyer un message pour la file d’attente `TestQueue` créée plus haut dans l’espace de noms `HowToSample` :

```
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Les messages envoyés aux files d’attente Service Bus (et reçus de celle-ci) sont des instances de la classe [BrokeredMessage][BrokeredMessage] . Les objets [BrokeredMessage][BrokeredMessage] possèdent un ensemble de propriétés standard (telles que [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu’un corps de données d’application arbitraires. Une application peut définir le corps du message en transmettant un objet sérialisable au constructeur de l’objet [BrokeredMessage][BrokeredMessage] ; le sérialiseur approprié est alors utilisé pour sérialiser l’objet. Vous pouvez également fournir un objet **java.IO.InputStream**.

L’exemple suivant montre comment envoyer cinq messages de test au **MessageSender** `TestQueue` obtenu dans l’extrait de code précédent :

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Les files d’attente Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go.

## <a name="receive-messages-from-a-queue"></a>Réception des messages d'une file d'attente
Le moyen principal pour recevoir des messages d’une file d’attente consiste à utiliser un objet **ServiceBusContract**. Ces messages reçus peuvent fonctionner dans deux modes différents : **ReceiveAndDelete** et **PeekLock**.

Lorsque le mode **ReceiveAndDelete** est utilisé, la réception est une opération unique : quand Service Bus reçoit une demande de lecture pour un message figurant dans une file d’attente, il marque le message comme étant consommé et le renvoie à l’application. Le mode **ReceiveAndDelete** (qui est le mode par défaut) est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d’un message en cas d’échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter.
Comme Service Bus a marqué le message comme étant consommé, lorsque l’application redémarre et recommence à consommer des messages, elle manque le message consommé avant l’incident.

En mode **PeekLock**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant **Delete** pour le message reçu. Lorsque Service Bus obtient l’appel **Delete**, il marque le message comme étant consommé et le supprime de la file d’attente.

L’exemple suivant montre comment les messages peuvent être reçus et traités à l’aide du mode **PeekLock** (qui n’est pas le mode par défaut). Dans cet exemple, une boucle infinie est créée et les messages sont traités à mesure qu'ils parviennent à la file d'attente « TestQueue » :

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d’application et des messages illisibles
Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlockMessage** pour le message reçu (au lieu de la méthode **deleteMessage**). Service Bus déverrouille alors le message dans la file d’attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu’un message verrouillé dans une file d’attente est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l’application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l’application subit un incident après le traitement du message, mais avant l’émission de la demande **deleteMessage**, le message est à nouveau remis à l’application lorsqu’elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Ceci est souvent obtenu grâce à la propriété **getMessageId** du message, qui reste constante pendant les tentatives de remise.

## <a name="next-steps"></a>Étapes suivantes
Les principes de base des files d’attente Service Bus étant appris, consultez [Files d’attente, rubriques et abonnements][] pour plus d’informations.

Pour plus d’informations, consultez le [Centre pour développeurs Java](/develop/java/).

[Kit de développement logiciel (SDK) Azure pour Java]: http://azure.microsoft.com/develop/java/
[Kit de ressources Azure pour Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[Files d’attente, rubriques et abonnements.]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx




<!--HONumber=Oct16_HO2-->


