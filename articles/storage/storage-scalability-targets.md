<properties
   pageTitle="Objectifs de performance et d’extensibilité Azure Storage | Microsoft Azure"
   description="Obtenez plus d’informations sur les objectifs d’extensibilité et de performances pour Azure Storage, y compris la capacité, le taux de demande et la bande passante entrante et sortante pour les comptes de stockage standard et premium. Découvrez les objectifs de performances des partitions dans chacun des services Azure Storage."
   services="storage"
   documentationCenter="na"
   authors="robinsh"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="04/19/2016"
   ms.author="robinsh" />

# Objectifs de performance et évolutivité d'Azure Storage

## Vue d'ensemble

Cet article décrit l’extensibilité et les performances de Microsoft Azure Storage. Pour prendre connaissance des autres informations relatives aux limites Azure, consultez [Limites, quotas et contraintes applicables aux services et abonnements Azure](../azure-subscription-service-limits.md).

>[AZURE.NOTE] Tous les comptes de stockage s’exécutent sur la nouvelle topologie de réseau plat et prennent en charge les objectifs d’extensibilité et de performances décrits ci-après, quel que soit le moment où ils ont été créés. Pour plus d'informations sur l'architecture de réseau plat Azure Storage et sur son extensibilité, consultez le billet de blog [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

>[AZURE.IMPORTANT] Les objectifs d’extensibilité et de performances répertoriés ici sont des objectifs haut de gamme mais réalisables. Dans tous les cas, le taux de demande et la bande passante atteints par votre compte de stockage dépendent de la taille des objets stockés, des modèles d’accès utilisés et du type de charge de travail de votre application. Veillez à tester votre service afin de déterminer si ses performances répondent à vos besoins. Dans la mesure du possible, évitez les pics soudains de trafic et assurez-vous que le trafic est bien réparti sur toutes les partitions.

>Lorsque votre application atteint la limite de gestion d’une partition concernant la charge de travail, Azure Storage commence à renvoyer des codes d’erreur 503 (Serveur occupé) ou 500 (Délai d’expiration de l’opération). Quand cela se produit, l’application doit utiliser une stratégie d’interruption exponentielle pour les nouvelles tentatives. L’interruption exponentielle diminue la charge sur la partition et atténue les pics de trafic pour cette partition.

Si les besoins de votre application dépassent les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez concevoir votre application afin qu’elle utilise plusieurs comptes de stockage et partitionner vos objets de données sur ces comptes. Pour plus d’informations sur la tarification des licences en volume, consultez la page [Prix appliqués à Azure Storage](https://azure.microsoft.com/pricing/details/storage/).


## Objectifs d'évolutivité pour les objets Blob, les files d'attente, les tables et les fichiers

[AZURE.INCLUDE [azure-storage-limits](../../includes/azure-storage-limits.md)]

## Objectifs d'évolutivité pour les disques de machines virtuelles

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../../includes/azure-storage-limits-vm-disks.md)]

Pour plus d’informations, consultez la page [Tailles des machine virtuelles dans Azure (Windows)](../virtual-machines/virtual-machines-windows-sizes.md) ou [Tailles des machines virtuelles dans Azure (Linux)](../virtual-machines/virtual-machines-linux-sizes.md).

### Comptes de stockage standard

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../../includes/azure-storage-limits-vm-disks-standard.md)]

### Comptes de stockage Premium

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../../includes/azure-storage-limits-vm-disks-premium.md)]

## Objectifs d'évolutivité pour Azure Resource Manager

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

## Partitions dans Azure Storage

Chaque objet contenant des données stockées dans Azure Storage (blobs, messages, entités et fichiers) appartient à une partition et est identifié par une clé de partition. La partition détermine la manière dont Azure Storage équilibre la charge des blobs, des messages, des entités et des fichiers sur les serveurs pour répondre aux besoins de trafic de ces objets. La clé de partition est unique. Elle permet de localiser un blob, un message ou une entité.

Le tableau présenté plus haut dans la section [Objectifs d'évolutivité des comptes de stockage standard](#standard-storage-accounts) répertorie les objectifs de performance pour une seule partition pour chaque service.

Les partitions affectent l’extensibilité et l’équilibrage de charge de chacun des services de stockage comme suit :

- **Blobs** : la clé de partition d’un blob est le nom du compte + le nom du conteneur + le nom du blob. Cela signifie que chaque objet peut avoir sa propre partition si la charge sur le blob en fait la demande. Les blobs peuvent être répartis sur plusieurs serveurs afin d’offrir un accès horizontal. Toutefois, un blob donné ne peut être servi que par un seul serveur. Si les blobs peuvent être regroupés de manière logique dans des conteneurs, ce regroupement n’a aucune incidence sur le partitionnement.

- **Fichiers** : la clé de partition pour un fichier est le nom du compte + le nom de partage du fichier. Cela signifie que tous les fichiers dans un partage de fichiers sont également dans une seule partition.

- **Messages** : la clé de partition d’un message est le nom du compte + le nom de la file d’attente : tous les messages d’une file d’attente sont regroupés en une seule partition et servis par un seul serveur. Plusieurs files d’attente peuvent être traitées par différents serveurs pour équilibrer la charge, quel que soit le nombre de files d’attente du compte de stockage.

- **Entités** : la clé de partition d’une entité est le nom du compte + le nom de la table + la clé de partition, cette dernière correspondant à la valeur de la propriété **PartitionKey** requise et définie par l’utilisateur pour l’entité. Toutes les entités affichant la même valeur de clé de partition sont regroupées dans la même partition et sont servies sur le même serveur de partition. Il s’agit d’un point important de la conception de votre application. Votre application doit offrir un parfait équilibre entre les avantages de l’extensibilité de la répartition des entités sur plusieurs partitions et les avantages d’accès aux données du regroupement des entités en une seule partition.

Grâce au regroupement d’un ensemble d’entités d’une table en une seule partition, il est possible de mener des opérations atomiques par lots sur les entités d’une même partition, car une partition existe sur un serveur unique. Ainsi, si vous souhaitez effectuer des opérations par lot sur un groupe d’entités, pensez à les regrouper avec la même clé de partition.

Par contre, les entités qui se trouvent dans la même table mais qui ont différentes clés de partition peuvent être réparties sur plusieurs serveurs, ce qui permet de disposer d’une meilleure extensibilité.

Des recommandations détaillées pour la conception d’une stratégie de partitionnement pour des tables sont disponibles [ici](https://msdn.microsoft.com/library/azure/hh508997.aspx).

## Voir aussi

- [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
- [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md)
- [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure.](storage-premium-storage.md)
- [Réplication Azure Storage](storage-redundancy.md)
- [Liste de contrôle des performances et de l’évolutivité de Microsoft Azure Storage](storage-performance-checklist.md)
- [Microsoft Azure Storage : service de stockage sur le cloud à haute disponibilité et à cohérence forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

<!---HONumber=AcomDC_0518_2016-->