<properties
	pageTitle="Vue d’ensemble des niveaux tarifaires de messagerie Service Bus Premium et Standard | Microsoft Azure"
	description="Messagerie Service Bus Premium et Standard"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="darosa"/>

# Couches messagerie Service Bus Premium et Standard 

La messagerie répartie Service Bus, qui comprend des entités de messagerie telles que les files d’attente et les rubriques, combine des fonctionnalités de messagerie d’entreprise à une sémantique riche de publication et d’abonnement à l’échelle du cloud. La messagerie répartie Service Bus sert d’infrastructure de communication à de nombreuses solutions cloud sophistiquées.

En introduisant la couche *Premium* de la messagerie Service Bus, nous répondons aux demandes couramment formulées par les clients concernant la mise à l’échelle, les performances et la disponibilité de leurs applications critiques. Bien qu’offrant des ensembles de fonctionnalités quasiment identiques, les deux couches de messagerie Azure Service Bus sont conçues pour différents cas de figure.

Les principales différences sont répertoriées dans le tableau ci-dessous.

| Premium | Standard |
|---------------------------------------|--------------------------------|
| Débit élevé | Débit variable |
| Performances prévisibles | Latence variable |
| Tarification prévisible | Tarification à l’utilisation variable |
| Possibilité de faire évoluer la charge de travail | N/A |

La **messagerie Azure Service Bus Premium** isole les ressources au niveau des couches processeur et mémoire, ce qui permet d’exécuter chaque charge de travail client de manière isolée. Ce conteneur de ressources est appelé une *unité de messagerie*. Au moins une unité de messagerie est allouée à chaque espace de noms premium. Vous pouvez acheter une, deux ou quatre unités de messagerie pour chaque espace de noms Service Bus Premium. Une entité ou une charge de travail unique peut couvrir plusieurs unités de messagerie et le nombre d’unités de messagerie peut être modifié à volonté. En revanche, les frais qui vous sont facturés sont établis par période de 24 heures. Au final, les performances de votre solution Service Bus sont non seulement prévisibles et répétables,

mais aussi supérieures. La messagerie Azure Service Bus Premium s’appuie sur le moteur de stockage introduit dans [Azure Event Hubs](http://azure.microsoft.com/services/event-hubs/). La messagerie Premium offre des performances de pointe bien supérieures à celle de la couche Standard.

## Différences techniques de la messagerie Premium

Voici quelques différences entre les couches de messagerie Standard et Premium.

### Entités partitionnées

Les entités partitionnées sont prises en charge dans la messagerie Premium, mais elles ne fonctionnent pas de la même manière que dans les couches Standard et De base de la messagerie Service Bus. La messagerie Premium n’utilise pas SQL comme magasin de données et ne permet plus la concurrence de ressources associée à une plateforme partagée. Par conséquent, le partitionnement n’est pas nécessaire. En outre, le nombre de partitions est passé de 16 partitions dans la messagerie Standard à deux partitions dans la messagerie Premium. Si le fait d’avoir deux partitions garantit la disponibilité, ce nombre est aussi mieux adapté à l’environnement d’exécution Premium. Pour plus d’informations, consultez [Partitionnement des entités de messagerie](service-bus-partitioning.md).

### Entités Express

La messagerie Premium s’exécutant dans un environnement d’exécution complètement isolé, les entités express n’ont plus lieu d’être. Par conséquent, les entités express ne sont pas prises en charge dans les espaces de noms Premium. Pour plus d’informations sur la fonctionnalité express, consultez la propriété [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx).

## Étapes suivantes

Pour en savoir plus sur la messagerie Service Bus, consultez les rubriques suivantes.

- [Présentation de la messagerie Azure Service Bus Premium (billet de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Présentation de la messagerie Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
- [Présentation de l'architecture d'Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Utilisation des files d’attente Service Bus](service-bus-dotnet-how-to-use-queues.md)

<!---HONumber=Oct15_HO4-->