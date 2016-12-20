---
title: FAQ Service Bus | Microsoft Docs
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
ms.date: 10/04/2016
ms.author: sethm;juconway
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b350bac36f1d46c97da37a2807ead8c9c732d69a


---
# <a name="service-bus-faq"></a>FAQ Service Bus
Cet article répond à certaines questions fréquemment posées sur Microsoft Azure Service Bus. Vous pouvez également visiter le [FAQ du support Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour obtenir des informations de support et de tarification générale Azure. Cet article contient les rubriques suivantes :

* [Questions générales sur la messagerie Azure Service Bus](#general-questions-about-azure-service-bus-messaging)
* [Meilleures pratiques Service Bus](#service-bus-best-practices)
* [Tarification de Service Bus](#service-bus-pricing)
* [Quotas Service Bus](#service-bus-quotas)
* [Gestion des abonnements et des espaces de noms](#subscription-and-namespace-management)
* [Dépannage](#service-bus-troubleshooting)

## <a name="general-questions-about-azure-service-bus-messaging"></a>Questions générales sur la messagerie Azure Service Bus
### <a name="what-is-azure-service-bus-messaging"></a>Présentation de la messagerie Azure Service Bus
La [messagerie Azure Service Bus](service-bus-messaging-overview.md) est une plateforme de messagerie cloud asynchrone qui vous permet d’envoyer des données entre systèmes dissociés. Microsoft propose cette fonctionnalité en tant que service, ce qui signifie que vous n’avez pas besoin d’héberger votre propre matériel pour l’utiliser.

### <a name="what-is-a-service-bus-namespace"></a>Présentation des espaces de noms Service Bus
Un [espace de nom](service-bus-create-namespace-portal.md) fournit un conteneur d’étendue pour l’adressage des ressources Service Bus au sein de votre application. Il est nécessaire d’en créer un pour utiliser Service Bus. C’est l’une des premières étapes à effectuer au démarrage.

### <a name="what-is-an-azure-service-bus-queue"></a>Présentation des files d’attente Azure Service Bus
Une [file d’attente Service Bus](service-bus-queues-topics-subscriptions.md) est une entité dans laquelle les messages sont stockés. Les files d’attente sont particulièrement utiles lorsque vous disposez de plusieurs applications ou de plusieurs parties d’une application distribuée qui doivent communiquer entre elles. La file d’attente est comparable à un centre de distribution, dans la mesure où plusieurs produits (messages) sont reçus, puis envoyés à partir de cet emplacement.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Présentation des rubriques et des abonnements Azure Service Bus
Une rubrique peut être visualisée comme une file d’attente. En cas d’utilisation de plusieurs abonnements, elle devient un modèle de messagerie plus riche ; plus simplement, il s’agit d’un outil de communication de type un-à-plusieurs. Ce modèle de publication/abonnement (ou *pub/sub*) permet à une application d’envoyer un message à une rubrique avec plusieurs abonnements pour que ce message soit reçu par plusieurs applications.

### <a name="what-is-a-partitioned-entity"></a>Présentation des entités partitionnées
Une file d’attente ou une rubrique classique est gérée par un seul courtier de messages et stockée dans une seule banque de messagerie. Les [files d’attente et rubriques partitionnées](service-bus-partitioning.md) sont gérées par plusieurs courtiers de messages et stockées dans plusieurs banques de messagerie. Cela signifie que le débit global d’une file d’attente ou d’une rubrique partitionnée n’est plus limité par les performances d’un seul courtier de messages ou d’une seule banque de messagerie. En outre, la panne temporaire d’une banque de messagerie ne rend pas une rubrique ou une file d’attente partitionnée indisponible.

Notez que le classement n’est pas garanti lors de l’utilisation d’entités partitionnées. Si une partition n’est pas disponible, vous pouvez toujours envoyer et recevoir des messages des autres partitions.

## <a name="service-bus-best-practices"></a>Meilleures pratiques Service Bus
### <a name="what-are-some-azure-service-bus-best-practices"></a>Présentation des meilleures pratiques Azure Service Bus
* [Meilleures pratiques relatives aux améliorations de performances à l’aide de la messagerie répartie Service Bus][Meilleures pratiques relatives aux améliorations de performances à l’aide de la messagerie répartie Service Bus] : cet article explique comment optimiser les performances lors de l’échange de messages répartis.

### <a name="what-should-i-know-before-creating-messaging-entities"></a>Informations à connaître avant de créer des entités de messagerie
Les propriétés suivantes de file d’attente et de rubrique sont immuables. Veuillez en tenir compte lorsque vous configurez vos entités, dans la mesure où elles ne peuvent pas être modifiées sans créer une nouvelle entité de remplacement.

* Taille
* Partitionnement
* Sessions
* Détection des doublons
* Entité rapide

## <a name="service-bus-pricing"></a>Tarification Service Bus
Cette section répond à certaines questions fréquemment posées sur la tarification de Service Bus. Vous pouvez également visiter le [Forum aux questions de Support Azure](http://go.microsoft.com/fwlink/?LinkID=185083) pour obtenir des informations de tarification générale Microsoft Azure. Pour obtenir toutes les informations sur la tarification Service Bus, voir la section [détails de tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="how-do-you-charge-for-service-bus"></a>Quel est le coût de Service Bus ?
Pour obtenir toutes les informations sur la tarification Service Bus, consultez la section [Détails de la tarification de Service Bus][Aperçu de la tarification]. Outre les prix indiqués, vous êtes facturé pour les transferts de données associés aux sorties à l’extérieur du centre de données dans lequel votre application est déployée.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Quelle est l’utilisation de Service Bus soumise au transfert de données ? Laquelle ne l’est pas ?
Les transferts de données au sein d’une région donnée d’Azure sont effectués gratuitement. Les transferts de données hors région sont soumis à des frais de sortie au tarif de 0,15 $ par Go depuis les régions Amérique du Nord et Europe et de 0,20 $ par Go depuis la région Asie-Pacifique. Les transferts de données entrants sont fournis gratuitement.

### <a name="does-service-bus-charge-for-storage"></a>Service Bus facture-t-il le stockage ?
Non, Service Bus ne facture pas le stockage ? Toutefois, il existe un quota limitant la quantité maximale de données qui peuvent être conservées par la file d’attente/rubrique. Voir le Forum aux questions suivants.

## <a name="service-bus-quotas"></a>Quotas Service Bus
Pour obtenir la liste des limites et quotas Service Bus, consultez la section [Vue d’ensemble des quotas][Vue d’ensemble des quotas].

### <a name="does-service-bus-have-any-usage-quotas"></a>Service Bus fixe-t-il des quotas d’utilisation ?
Par défaut, pour n’importe quel service cloud, Microsoft définit un quota d’utilisation agrégée mensuel calculé avec tous les abonnements d’un client. Nous savons que vous pouvez avoir des besoins dépassant ces limites, veuillez contacter le service clientèle à tout moment pour que nous puissions déterminer vos besoins et ajuster ces limites en conséquence. Pour Service Bus, les quotas d’utilisation d’agrégation sont les suivants :

* 5 milliards de messages

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

## <a name="subscription-and-namespace-management"></a>Gestion des abonnements et des espaces de noms
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Migration d’un espace de noms vers un autre abonnement Azure
Vous pouvez utiliser les commandes PowerShell (voir [ici][ici]) pour déplacer un espace de noms d’un abonnement Azure à un autre. Pour exécuter cette opération, l’espace de noms doit être déjà actif. Il est également impératif que l’utilisateur qui exécute les commandes soit un administrateur des abonnements source et cible.

## <a name="service-bus-troubleshooting"></a>Résolution des problèmes Service Bus
[Vue d’ensemble des exceptions][Vue d’ensemble des exceptions]

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-messaging-apis-and-their-suggested-actions"></a>Quelles sont les exceptions générées par les API de messagerie Azure Service Bus et les actions recommandées ?
Les API de messagerie peuvent générer des exceptions entrant dans les catégories suivantes :

* Erreur de codage de l’utilisateur
* Erreur d’installation ou de configuration
* Exceptions temporaires
* Autres exceptions

L’article [Exceptions de la messagerie Service Bus][Vue d’ensemble des exceptions] décrit certaines exceptions, ainsi que les actions suggérées.

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Qu’est-ce qu’une signature d’accès partagé et quels langages prennent en charge la génération d’une signature ?
Les signatures d’accès partagé sont un mécanisme d’authentification basé sur des hachages sécurisés SHA-256 ou des URI. Pour plus d’informations sur la génération de vos propres signatures en Node, PHP, Java et C\#, consultez l’article [Signatures d’accès partagé][Signatures d’accès partagé].

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la messagerie Service Bus, voir les rubriques suivantes.

* [Présentation de la messagerie Azure Service Bus Premium (billet de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Présentation de la messagerie Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
* [Présentation de l'architecture d'Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Meilleures pratiques relatives aux améliorations de performances à l’aide de la messagerie répartie Service Bus]: service-bus-performance-improvements.md
[Meilleures pratiques pour protéger les applications contre les pannes de Service Bus et les sinistres]: service-bus-outages-disasters.md
[Aperçu de la tarification]: https://azure.microsoft.com/pricing/details/service-bus/
[Vue d’ensemble des quotas]: service-bus-quotas.md
[ici]: service-bus-powershell-how-to-provision.md#migrate-a-namespace-to-another-azure-subscription
[Vue d’ensemble des exceptions]: service-bus-messaging-exceptions.md
[Signatures d’accès partagé]: service-bus-sas-overview.md



<!--HONumber=Nov16_HO3-->


