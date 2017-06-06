---
title: "Vue d’ensemble des niveaux tarifaires de messagerie Azure Service Bus Premium et Standard | Microsoft Docs"
description: Couches messagerie Service Bus Premium et Standard
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: 
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/28/2017
ms.author: darosa;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: a916f643c7e6727d6053865d1c0bd2f683a53b3f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Couches messagerie Service Bus Premium et Standard

La messagerie Service Bus, qui comprend des entités telles que les files d’attente et les rubriques, associe des fonctionnalités de messagerie d’entreprise à une sémantique riche de publication et d’abonnement à l’échelle du cloud. La messagerie Service Bus sert d’infrastructure de communication à de nombreuses solutions cloud sophistiquées.

La couche *Premium* de la messagerie Service Bus répond aux demandes couramment formulées par les clients concernant la mise à l’échelle, les performances et la disponibilité de leurs applications critiques. Bien qu’offrant des ensembles de fonctionnalités quasiment identiques, les deux couches de messagerie Service Bus sont conçues pour différents cas de figure.

Les principales différences sont répertoriées dans le tableau suivant.

| Premium | Standard |
| --- | --- |
| Débit élevé |Débit variable |
| Performances prévisibles |Latence variable |
| Prix fixe |Tarification à l’utilisation variable |
| Possibilité de faire évoluer la charge de travail |N/A |
| Taille de message maximale de 1 Mo |Taille de message maximale de 256 Ko |

La **messagerie Service Bus Premium** isole les ressources au niveau processeur et mémoire, ce qui permet d’exécuter chaque charge de travail client de manière isolée. Ce conteneur de ressources est appelé *unité de messagerie*. Au moins une unité de messagerie est allouée à chaque espace de noms premium. Vous pouvez acheter une, deux ou quatre unités de messagerie pour chaque espace de noms Service Bus Premium. Une entité ou une charge de travail unique peut couvrir plusieurs unités de messagerie et le nombre d’unités de messagerie peut être modifié à volonté. En revanche, les frais qui vous sont facturés sont établis par période de 24 heures. Au final, les performances de votre solution Service Bus sont non seulement prévisibles et répétables,

Au final, les performances de votre solution Service Bus sont non seulement prévisibles et répétables, mais aussi supérieures. La messagerie Service Bus Premium s’appuie sur le moteur de stockage introduit dans [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). La messagerie Premium offre des performances de pointe bien supérieures à celles de la couche Standard.

## <a name="premium-messaging-technical-differences"></a>Différences techniques de la messagerie Premium

Les sections suivantes décrivent les quelques différences entre les couches de messagerie Standard et Premium.

### <a name="partitioned-queues-and-topics"></a>Files d’attente et rubriques partitionnées

Les files d’attente et rubriques partitionnées sont prises en charge dans la messagerie Premium ; en fait ces entités sont toujours partitionnées (et ne peut pas être désactivées). Toutefois, les files d’attente et les rubriques partitionnées Premium ne fonctionnent pas de la même manière que dans les couches Standard et De base de la messagerie Service Bus. La messagerie Premium n’utilise pas SQL comme magasin de données et ne permet plus la concurrence de ressources associée à une plateforme partagée. Par conséquent, le partitionnement n’est pas nécessaire pour améliorer les performances. En outre, le nombre de partitions est passé de 16 partitions dans la messagerie Standard à 2 partitions dans la messagerie Premium. Si le fait d’avoir deux partitions garantit la disponibilité, ce nombre est aussi mieux adapté à l’environnement d’exécution Premium. 

Avec la messagerie Premium, lorsque vous spécifiez la taille d’une entité avec [MaxSizeInMegabytes](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxsizeinmegabytes#Microsoft_ServiceBus_Messaging_QueueDescription_MaxSizeInMegabytes), cette taille est divisée de manière égale entre les 2 partitions, contrairement aux [entités partitionnées Standard](service-bus-partitioning.md#standard) dans lesquelles la taille totale correspond à 16 fois la taille spécifiée. 

Pour plus d’informations sur le partitionnement, voir [Files d’attentes et rubriques partitionnées](service-bus-partitioning.md).

### <a name="express-entities"></a>Entités Express

La messagerie Premium s’exécutant dans un environnement d’exécution complètement isolé, les entités express ne sont pas prises en charge dans les espaces de noms Premium. Pour en savoir plus sur la fonctionnalité express, consultez la propriété [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Si vous avez du code s’exécutant dans la messagerie Standard et que vous souhaitez le faire passer au niveau Premium, vérifiez que la propriété [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) est définie sur **false** (la valeur par défaut).

## <a name="get-started-with-premium-messaging"></a>Prise en main de Premium Messaging

La prise en main de Premium Messaging est immédiate ; le processus est similaire à celui de la messagerie Standard. Commencez par [créer un espace de noms](service-bus-create-namespace-portal.md). Veillez à sélectionner **Premium** sous **Niveau de tarification**.

![create-premium-namespace][create-premium-namespace]

Vous pouvez également créer des [espaces de noms Premium à l’aide de modèles Azure Resource Manager](https://azure.microsoft.com/en-us/resources/templates/101-servicebus-pn-ar/).


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la messagerie Service Bus, consultez les rubriques suivantes.

* [Introducing Azure Service Bus Premium Messaging (Présentation de la messagerie Azure Service Bus Premium (billet de blog))](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introducing Azure Service Bus Premium Messaging (Présentation de la messagerie Azure Service Bus Premium (Channel9))](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Service Bus messaging: flexible data delivery in the cloud (Messagerie Service Bus : service flexible de distribution de données dans le cloud)](service-bus-messaging-overview.md)
* [Utilisation des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png

