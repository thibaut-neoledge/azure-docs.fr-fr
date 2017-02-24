---
title: Forum Aux Questions (FAQ) sur Azure Service Bus | Microsoft Docs
description: "Répond à certaines questions fréquemment posées sur Azure Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: cc75786d-3448-4f79-9fec-eef56c0027ba
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: da7f8e3e61705cf07ff65c9dd1d8f292f4fb9f62
ms.openlocfilehash: 9061829e42ed5563d64860774aa7d80f2ab011bd


---
# <a name="service-bus-faq"></a>FAQ Service Bus
Cet article répond à certaines questions fréquemment posées sur Microsoft Azure Service Bus. Vous pouvez également visiter le [FAQ du support Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour obtenir des informations de support et de tarification générale Azure.

## <a name="general-questions-about-azure-service-bus"></a>Questions générales sur Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Qu’est-ce qu’Azure Service Bus ?
[Azure Service Bus](service-bus-messaging-overview.md) est une plateforme cloud de messagerie asynchrone qui vous permet d’envoyer des données entre systèmes découplés. Microsoft propose cette fonctionnalité en tant que service, ce qui signifie que vous n’avez pas besoin d’héberger votre propre matériel pour l’utiliser.

### <a name="what-is-a-service-bus-namespace"></a>Présentation des espaces de noms Service Bus
Un [espace de nom](service-bus-create-namespace-portal.md) fournit un conteneur d’étendue pour l’adressage des ressources Service Bus au sein de votre application. Il est nécessaire d’en créer un pour utiliser Service Bus. C’est l’une des premières étapes à effectuer au démarrage.

### <a name="what-is-an-azure-service-bus-queue"></a>Présentation des files d’attente Azure Service Bus
Une [file d’attente Service Bus](service-bus-queues-topics-subscriptions.md) est une entité dans laquelle les messages sont stockés. Les files d’attente sont particulièrement utiles lorsque vous disposez de plusieurs applications ou de plusieurs parties d’une application distribuée qui doivent communiquer entre elles. La file d’attente est comparable à un centre de distribution, dans la mesure où plusieurs produits (messages) sont reçus, puis envoyés à partir de cet emplacement.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Présentation des rubriques et des abonnements Azure Service Bus
Une rubrique peut être visualisée comme une file d’attente. En cas d’utilisation de plusieurs abonnements, elle devient un modèle de messagerie plus riche ; plus simplement, il s’agit d’un outil de communication de type un-à-plusieurs. Ce modèle de publication/abonnement (ou *pub/sub*) permet à une application d’envoyer un message à une rubrique avec plusieurs abonnements pour que ce message soit reçu par plusieurs applications.

### <a name="what-is-a-partitioned-entity"></a>Présentation des entités partitionnées
Une file d’attente ou une rubrique classique est gérée par un seul courtier de messages et stockée dans une seule banque de messagerie. Les [files d’attente et rubriques partitionnées](service-bus-partitioning.md) sont gérées par plusieurs courtiers de messages et stockées dans plusieurs banques de messagerie. Cela signifie que le débit global d’une file d’attente ou d’une rubrique partitionnée n’est plus limité par les performances d’un seul courtier de messages ou d’une seule banque de messagerie. En outre, la panne temporaire d’une banque de messagerie ne rend pas une rubrique ou une file d’attente partitionnée indisponible.

Notez que le classement n’est pas garanti lors de l’utilisation d’entités partitionnées. Si une partition n’est pas disponible, vous pouvez toujours envoyer et recevoir des messages des autres partitions.

## <a name="best-practices"></a>Meilleures pratiques
### <a name="what-are-some-azure-service-bus-best-practices"></a>Présentation des meilleures pratiques Azure Service Bus
* [Meilleures pratiques relatives aux améliorations de performances avec Service Bus][Best practices for performance improvements using Service Bus] : cet article explique comment optimiser les performances de l’échange de messages.

### <a name="what-should-i-know-before-creating-entities"></a>Quelles sont les informations à connaître pour pouvoir créer des entités ?
Les propriétés suivantes de file d’attente et de rubrique sont immuables. Veuillez en tenir compte lorsque vous configurez vos entités, dans la mesure où elles ne peuvent pas être modifiées sans créer une nouvelle entité de remplacement.

* Taille
* Partitionnement
* Sessions
* Détection des doublons
* Entité rapide

## <a name="pricing"></a>Tarification
Cette section répond à certaines questions fréquemment posées sur la tarification de Service Bus. Vous pouvez également visiter le [Forum aux questions de Support Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour obtenir des informations de tarification générale Microsoft Azure. Pour obtenir toutes les informations sur la tarification Service Bus, voir la section [détails de tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-service-bus"></a>Quel est le coût de Service Bus ?
Pour obtenir toutes les informations sur la tarification de Service Bus, consultez les [détails sur la tarification de Service Bus][Pricing overview]. Outre les prix indiqués, vous êtes facturé pour les transferts de données associés aux sorties à l’extérieur du centre de données dans lequel votre application est déployée.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Quelle est l’utilisation de Service Bus soumise au transfert de données ? Laquelle ne l’est pas ?
Les transferts de données au sein d’une région Azure donnée sont effectués gratuitement, de même que les transferts de données entrants. Le transfert de données en dehors d’une région est soumis à des frais de sortie consultables [ici](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Service Bus facture-t-il le stockage ?
Non, Service Bus ne facture pas le stockage ? Toutefois, il existe un quota limitant la quantité maximale de données qui peuvent être conservées par la file d’attente/rubrique. Voir le Forum aux questions suivants.

## <a name="quotas"></a>Quotas
Pour obtenir la liste des limites et quotas Service Bus, consultez [Vue d’ensemble des quotas][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Service Bus fixe-t-il des quotas d’utilisation ?
Par défaut, pour n’importe quel service cloud, Microsoft définit un quota d’utilisation agrégée mensuel calculé avec tous les abonnements d’un client. Nous savons que vous pouvez avoir des besoins dépassant ces limites, veuillez contacter le service clientèle à tout moment pour que nous puissions déterminer vos besoins et ajuster ces limites en conséquence. Pour Service Bus, les quotas d’utilisation agrégés s’élèvent à 5 milliards de messages par mois.

Nous nous réservons le droit de désactiver un compte client ayant dépassé son quota d’utilisation pour un mois donné. Nous enverrons une notification par messagerie électronique pour contacter le client avant d’exécuter une action quelconque. Les clients qui dépassent ces quotas sont responsables des frais qui dépassent ces quotas.

À l’instar d’autres services sur Azure, Service Bus applique un ensemble de quotas spécifiques pour assurer une utilisation juste des ressources. Les quotas d’utilisation appliqués par le service sont les suivantes :

#### <a name="queuetopic-size"></a>Taille de la file d’attente/rubrique
Vous spécifiez la taille maximale de file d’attente ou de rubrique lors de la création de la file d’attente ou de la rubrique. Ce quota peut avoir une valeur de 1, 2, 3, 4 ou 5 Go. Si la taille de la file maximale est atteinte, les messages entrants supplémentaires sont rejetés et le code appelant reçoit une exception.

#### <a name="naming-restrictions"></a>Restrictions concernant l’attribution de noms
Un nom d’espace de noms Service Bus peut uniquement contenir entre 6 et 50 caractères. Le nombre limite de caractères pour chaque file d’attente, rubrique ou abonnement est compris entre 1 et 50 caractères.

#### <a name="number-of-concurrent-connections"></a>Nombre de connexions simultanées
File d’attente/Rubrique/Abonnement - le nombre de connexions TCP simultanées sur une file d’attente/rubrique/abonnement est limité à 100. Si ce quota est atteint, les requêtes suivantes pour des connexions supplémentaires sont rejetées et une exception sera reçue par le code appelant. Pour chaque structure de messagerie, Service Bus entretient une connexion TCP si l’un des clients créés par cette structure de messagerie ont une opération active en attente ou une opération terminée depuis moins de 60 secondes. Les opérations REST ne sont pas comptées parmi les connexions TCP simultanées.

#### <a name="number-of-topicsqueues-per-service-namespace"></a>Nombre de rubriques/files d’attente par espace de noms de service
Le nombre maximal de rubriques/files d’attente (entités soutenues par un stockage durable) sur un espace de noms de service est limité à 10 000. Si ce quota est atteint, les demandes de création de nouvelle rubrique/file d’attente dans l’espace de noms suivantes sont rejetées. Dans ce cas, le portail Azure Classic affiche un message d’erreur ou le code client appelant reçoit une exception, selon que la tentative de création a été effectuée par le biais du portail ou dans le code client.

### <a name="message-size-quotas"></a>Quotas de taille des messages
#### <a name="queuetopicsubscription"></a>File d’attente/Rubrique/Abonnement
**Taille de message** – chaque message est limité au total à 256 Ko, en-têtes de message compris.

**Taille de l’en-tête de message** : chaque en-tête de message est limité à 64 Ko.

Les messages qui dépassent ces quotas de taille sont rejetés et le code appelant reçoit une exception.

**Nombre d’abonnements par rubrique** : le nombre maximal d’abonnements par rubrique est limité à 2 000. Si ce quota est atteint, les demandes de création d’abonnements supplémentaires à la rubrique seront rejetées. Dans ce cas, le portail Azure Classic affiche un message d’erreur ou le code client appelant reçoit une exception, selon que la tentative de création a été effectuée par le biais du portail ou dans le code client.

**Nombre de filtres SQL par rubrique** : le nombre maximal de filtres SQL par rubrique est limité à 2 000. Si ce quota est atteint, les requêtes de création de filtres supplémentaires sur la rubrique suivantes sont rejetées et une exception est reçue par le code appelant.

**Nombre de filtres de corrélation par rubrique** : le nombre maximal de filtres de corrélation par rubrique est limité à 100 000. Si ce quota est atteint, les requêtes de création de filtres supplémentaires sur la rubrique suivantes sont rejetées et une exception est reçue par le code appelant.

## <a name="troubleshooting"></a>Résolution des problèmes
### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Quelles sont les exceptions générées par les API Azure Service Bus et les actions recommandées ?
Pour obtenir la liste des exceptions Service Bus potentielles, consultez la page [Vue d’ensemble des exceptions][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Qu’est-ce qu’une signature d’accès partagé et quels langages prennent en charge la génération d’une signature ?
Les signatures d’accès partagé sont un mécanisme d’authentification basé sur des hachages sécurisés SHA-256 ou des URI. Pour plus d’informations sur la génération de vos propres signatures en Node, PHP, Java et C\#, consultez l’article [Signatures d’accès partagé][Shared Access Signatures].

## <a name="subscription-and-namespace-management"></a>Gestion des abonnements et des espaces de noms
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Migration d’un espace de noms vers un autre abonnement Azure
Sur le Portail Azure, vous pouvez migrer les espaces de noms Service Bus vers un autre abonnement en suivant les instructions présentées [ici](../azure-resource-manager/resource-group-move-resources.md#use-portal). Si vous préférez utiliser PowerShell, suivez les instructions ci-dessous : 

La séquence de commandes suivante déplace un espace de noms d'un abonnement Azure vers un autre. Pour exécuter cette opération, l'espace de noms doit être déjà actif, et l'utilisateur qui exécute les commandes PowerShell doit être administrateur des abonnements à la fois source et cible.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Service Bus, consultez les rubriques qui suivent.

* [Présentation d’Azure Service Bus Premium (billet de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Présentation d’Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Vue d’ensemble de Service Bus](service-bus-messaging-overview.md)
* [Présentation de l'architecture d'Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas-overview.md



<!--HONumber=Feb17_HO2-->


