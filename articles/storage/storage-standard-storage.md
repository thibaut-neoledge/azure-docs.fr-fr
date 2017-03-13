---
title: "Stockage Standard sur disque économique et disques de machines virtuelles Azure | Microsoft Docs"
description: "Présentation du stockage Standard économique et des disques de machine virtuelle gérés et non gérés."
services: storage
documentationcenter: 
author: yuemlu
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2017
ms.author: yuemlu
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: c208f44045ba414be2034f577435ae02ea4456cf
ms.lasthandoff: 02/27/2017


---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Stockage Standard économique et disques de machine virtuelle Azure gérés et non gérés

Le stockage Standard Azure permet une prise en charge fiable et économique des disques des machines virtuelles exécutant des charges de travail non sensibles aux latences. Il prend également en charge les objets blob, les tables, les files d’attente et les fichiers. Dans le cadre d’un stockage Standard, les données sont stockées sur des disques durs. Lorsque vous utilisez des machines virtuelles, vous pouvez utiliser des disques de stockage Standard pour les scénarios de développement/test et les charges de travail les moins critiques, et des disques de stockage Premium pour les applications de production critiques. Le stockage Standard est disponible dans toutes les régions Azure. 

Cet article porte sur l’utilisation du stockage Standard pour les disques de machines virtuelles. Pour plus d’informations sur l’utilisation du stockage avec les objets blob, les tables, les files d’attente et les fichiers, reportez-vous à [Introduction à Microsoft Azure Storage](storage-introduction.md).

## <a name="disk-types"></a>Types de disque

Il existe deux façons de créer des disques Standard pour les machines virtuelles Azure :

**Disques non gérés** : avec cette méthode d’origine, vous gérez les comptes de stockage utilisés pour stocker les fichiers VHD qui correspondent aux disques des machines virtuelles. Les fichiers VHD sont stockés en tant qu’objets blob de pages dans les comptes de stockage. Les disques non gérés peuvent être associés à n’importe quelle taille de machine virtuelle Azure, y compris les machines virtuelles qui utilisent principalement le stockage Premium, telles que les séries DSv2 et GS. Les machines virtuelles Azure prennent en charge l’association de plusieurs disques Standard, autorisant jusqu’à 64 To de stockage par machine virtuelle.

[**Disques gérés Azure**](storage-managed-disks-overview.md) : cette fonctionnalité gère les comptes de stockage que vous utilisez pour les disques de machines virtuelles. Vous spécifiez le type (Premium ou Standard) et la taille du disque dont vous avez besoin, et Azure crée et gère le disque pour vous. Vous n’avez pas à positionner les disques sur plusieurs comptes de stockage afin de garantir le respect des limites d’extensibilité des comptes de stockage : Azure le fait pour vous.

Même si les deux types de disques sont disponibles, nous vous recommandons d’utiliser des disques gérés pour tirer parti de leurs nombreuses fonctionnalités.

Pour une prise en main du stockage Standard Azure, consultez [Évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/) . 

Pour plus d’informations sur la création d’une machine virtuelle avec disques gérés, consultez l’un des articles suivants.

* [Créer une machine virtuelle à l’aide de Resource Manager et de PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)
* [Création d'une machine virtuelle Linux à l’aide d’Aide CLI 2.0](../virtual-machines/virtual-machines-linux-quick-create-cli.md)

## <a name="standard-storage-features"></a>Fonctionnalités du stockage Standard 

Examinons certaines des fonctionnalités du stockage Standard. Pour plus d’informations, consultez [Introduction à Microsoft Azure Storage](storage-introduction.md).

**Stockage Standard** : le stockage Standard Azure prend en charge les disques Azure, les objets blob Azure, les fichiers Azure, les tables Azure et les files d’attente Azure. Pour utiliser les services de stockage Standard, commencez par [Créer un compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account).

**Disques de stockage Standard :** les disques de stockage Standard peuvent être associés à toutes les machines virtuelles Azure, y compris les machines virtuelles de tailles utilisées avec le stockage Premium, comme les séries DSv2 et GS. Un disque de stockage Standard ne peut être associé qu’à une seule machine virtuelle. Toutefois, vous pouvez associer un ou plusieurs de ces disques à une machine virtuelle, jusqu’au nombre maximal de disques défini pour cette taille de machine virtuelle. La section suivante sur les objectifs de performance et d’extensibilité du stockage Standard décrit ces spécifications plus en détail. 

**Objet blob de pages Standard** : les objets blob de pages Standard sont utilisés pour stocker les disques persistants des machines virtuelles et sont également accessibles directement via REST comme d’autres types d’objets blob Azure. Les [objets blob de pages](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) sont une collection de pages de 512 octets optimisées pour les opérations de lecture et d’écriture aléatoires. 

**Réplication du stockage :** dans la plupart des régions, les données d’un compte de stockage Standard peuvent être répliquées localement ou géorépliquées entre plusieurs centres de données. Les quatre types de réplication disponibles sont le stockage localement redondant (LRS), le stockage redondant dans une zone (ZRS), le stockage géoredondant (GRS) et le stockage géoredondant avec accès en lecture (RA-GRS). Actuellement, les disques gérés dans le stockage Standard prennent seulement en charge le stockage localement redondant (LRS). Pour plus d'informations, consultez [Réplication Azure Storage](storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Cibles de performance et d’évolutivité

Dans cette section, nous allons décrire les objectifs de performances et d’extensibilité à prendre en considération lors de l’utilisation du stockage Standard.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Limites de compte : ne s’applique pas aux disques gérés

| **Ressource** | **Limite par défaut** |
|--------------|-------------------|
| To par compte de stockage  | 500 To |
| Entrée max.<sup>1</sup> par compte de stockage (régions des États-Unis) | 10 Gbit/s si GRS/ZRS est activé, 20 Gbit/s pour LRS |
| Sortie max.<sup>1</sup> par compte de stockage (régions des États-Unis) | 20 Gbit/s si RA-GRS/GRS/ZRS est activé, 30 Gbit/s pour LRS |
| Entrée max.<sup>1</sup> par compte de stockage (régions d’Europe et d’Asie) | 5 Gbit/s si GRS/ZRS est activé, 10 Gbit/s pour LRS |
| Sortie max.<sup>1</sup> par compte de stockage (régions d'Europe et d'Asie) | 10 Gbit/s si RA-GRS/GRS/ZRS est activé, 15 Gbit/s pour LRS |
| Taux de demandes total (objets de 1 Ko) par compte de stockage | Jusqu’à 20 000 opérations E/S, entités par seconde ou messages par seconde |

<sup>1</sup>Entrée désigne toutes les données (requêtes) envoyées à un compte de stockage. Sortie désigne toutes les données (réponses) reçues d’un compte de stockage.

Pour plus d'informations, consultez [Objectifs d'extensibilité et de performances d'Azure Storage](storage-scalability-targets.md).

Si les besoins de votre application dépassent les objectifs d’extensibilité d’un compte de stockage unique, générez votre application pour qu’elle utilise plusieurs comptes de stockage et partitionnez vos données sur ces comptes. Alternativement, vous pouvez utiliser des disques gérés Azure, auquel cas Azure gère pour vous le partitionnement et le positionnement de vos données.

### <a name="standard-disks-limits"></a>Limites des disques Standard

Contrairement aux disques Premium, les opérations d’entrée/sortie par seconde et le débit (bande passante) des disques Standard ne sont pas configurés. Les performances des disques Standard varient en fonction de la taille de la machine virtuelle à laquelle le disque est associé, pas de la taille du disque. Vous pouvez vous attendre aux limites de performance répertoriées dans le tableau ci-dessous.

**Limites des disques Standard (gérés et non gérés)**

| **Niveau Machine Virtuelle**            | **Niveau de base - Machine virtuelle** | **Niveau standard - Machine virtuelle** |
|------------------------|-------------------|----------------------|
| Taille maximale du disque          | 1 023 Go           | 1 023 Go              |
| Max 8 Ko d’E/S par seconde par disque | Jusqu’à 300         | Jusqu’à 500            |
| Bande passante maximale par disque | Jusqu’à 60 Mo/s     | Jusqu’à 60 Mo/s        |

Si votre charge de travail exige la prise en charge des disques hautes performances à faible latence, vous devez envisager d’utiliser le stockage Premium. Pour en savoir plus les avantages du stockage Premium, visitez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>Captures instantanées et copie d’objets blob

Pour le service de stockage, le fichier VHD est un objet blob de pages. Vous pouvez réaliser des captures instantanées d’objets blob de pages et les copier dans un autre emplacement, par exemple un compte de stockage différent.

### <a name="unmanaged-disks"></a>Disques non gérés

Vous pouvez créer des [captures instantanées incrémentielles](storage-incremental-snapshots.md) pour les disques Standard non gérés, de la même manière que vous utilisez des captures instantanées avec le stockage Standard. Si votre disque source se trouve dans un compte de stockage localement redondant, nous vous recommandons de créer des captures instantanées et de les copier sur un compte de stockage Standard géoredondant. Pour plus d'informations, consultez [Options de redondance du stockage Azure](storage-redundancy.md).

Si un disque est associé à une machine virtuelle, certaines opérations d’API ne sont pas autorisées sur les disques. Par exemple, vous ne pouvez pas effectuer une opération [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) sur cet objet blob, tant que le disque est attaché à une machine virtuelle. À la place, commencez par créer une capture instantanée de cet objet blob à l’aide de la méthode [Snapshot Blob](/rest/api/storageservices/fileservices/Snapshot-Blob) de l’API REST, puis exécutez l’opération [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob) de la capture instantanée pour copier le disque attaché. Vous pouvez également dissocier le disque et effectuer les opérations nécessaires.

Pour conserver des copies géoredondantes de vos captures instantanées, vous pouvez copier des captures instantanées d’un compte de stockage localement redondant vers un compte de stockage Standard géoredondant à l’aide des opérations AzCopy ou Copy Blob. Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) et [Copie d’un objet blob](/rest/api/storageservices/fileservices/Copy-Blob).

Pour plus d’informations sur l’exécution d’opérations REST sur les objets blob de pages dans les comptes de stockage Standard, consultez [API REST des services d’Azure Storage](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Disques gérés

Une capture instantanée d’un disque géré est une copie en lecture seule du disque géré qui est stockée comme un disque géré Standard. Pour le moment, les captures instantanées incrémentielles ne sont pas prises en charge pour les disques gérés, mais elles le seront dans le futur.

Si un disque géré est associé à une machine virtuelle, certaines opérations d’API ne sont pas autorisées sur les disques. Par exemple, vous ne pouvez pas générer une signature d’accès partagé (SAP) pour effectuer une opération de copie alors que le disque est associé à une machine virtuelle. Au lieu de cela, commencez par créer une capture instantanée du disque, puis réalisez-en une copie. Sinon, vous pouvez aussi dissocier le disque, puis générer une signature d’accès partagé (SAP) pour réaliser l’opération de copie.

## <a name="pricing-and-billing"></a>Tarification et facturation

Les considérations de facturation suivantes s’appliquent à l’utilisation du stockage Standard :

* Taille des données/disques non gérés du stockage standard 
* Disques gérés Standard
* Captures instantanées du stockage Standard
* Transferts de données sortantes
* Transactions

**Données de stockage et taille de disque non gérées :** pour les disques non gérés et les autres données (objets blob, tables, files d’attente et fichiers), vous êtes facturé uniquement pour la quantité d’espace utilisée. Par exemple, si vous avez une machine virtuelle dont les objets blob de pages sont configurés sur 127 Go, mais que la machine virtuelle n’utilise réellement que 10 Go d’espace, vous êtes facturé pour 10 Go d’espace. 

**Disques gérés :** les disques gérés sont facturés selon la taille configurée. Si votre disque est configuré comme un disque de 10 Go et que vous utilisez uniquement 5 Go, vous êtes tout de même facturé pour la taille configurée de 10 Go.

**Captures instantanées** : les captures instantanées des disques Standard sont facturées en fonction de la capacité supplémentaire utilisée par les captures instantanées. Pour plus d'informations sur les captures instantanées, consultez [Création d'un instantané d'objet blob](/rest/api/storageservices/fileservices/Creating-a-Snapshot-of-a-Blob).

**Transferts de données sortantes**: les [transferts de données sortantes](https://azure.microsoft.com/pricing/details/data-transfers/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

**Transaction** : Azure facture 0,0036 $ par tranche de 100 000 transactions de stockage standard. Les transactions comprennent les opérations de lecture et d’écriture pour le stockage.

Pour plus d’informations sur la tarification du stockage Standard, des machines virtuelles et des disques gérés, consultez :

* [Tarification d’Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Tarification des disques gérés](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Prise en charge du service Azure Backup 

Les machines virtuelles avec disques non gérés peuvent être sauvegardées à l’aide de Sauvegarde Azure. [Détails supplémentaires](../backup/backup-azure-vms-first-look-arm.md).

Vous pouvez également utiliser le service Sauvegarde Azure avec Managed Disks pour créer une tâche de sauvegarde avec des sauvegardes périodiques, une restauration facile des machines virtuelles et des stratégies de rétention de sauvegarde. Vous pouvez en savoir plus sur ce point dans [Using Azure Backup service for VMs with Managed Disks](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup) (Utilisation du service Azure Backup pour les machines virtuelles avec disques gérés).

## <a name="next-steps"></a>Étapes suivantes

* [Introduction à Azure Storage](storage-introduction.md)

* [Créer un compte de stockage](storage-create-storage-account.md)

* [Vue d’ensemble des disques gérés](storage-managed-disks-overview.md)

* [Créer une machine virtuelle à l’aide de Resource Manager et de PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

* [Création d'une machine virtuelle Linux à l’aide d’Aide CLI 2.0](../virtual-machines/virtual-machines-linux-quick-create-cli.md)
