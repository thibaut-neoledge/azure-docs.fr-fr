---
title: "Migrer des machines virtuelles Azure vers des disques gérés | Microsoft Docs"
description: "Migrez des machines virtuelles créées à l’aide de disques non gérés dans des comptes de stockage afin d’utiliser des disques gérés."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: e5f2eaa55e92c9a630533a5594e1a659cc2192f1
ms.openlocfilehash: 390195c8f07430e5fac6d53a77f6bb1aba53b197


---

# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrer des machines virtuelles Azure vers des disques gérés dans Azure

Avec les disques gérés Azure, il n’est plus nécessaire de gérer des [comptes de stockage](../storage/storage-introduction.md) pour les machines virtuelles Azure. Vous spécifiez simplement le type ([Premium](../storage/storage-premium-storage-performance.md) ou [Standard](../storage/storage-standard-storage.md)) et la taille de disque dont vous avez besoin, et Azure crée et gère le disque pour vous. 

Si vous utilisez actuellement l’option de stockage Standard pour vos disques, vous pouvez migrer vers des disques gérés Premium afin de tirer parti de leur vitesse et de leurs performances. Les [disques gérés Premium](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage-performance) reposent sur des disques SSD (Solide State Drive), qui assurent de hautes performances et une faible latence pour les machines virtuelles dont les charges de travail nécessitent de nombreuses E/S.

Vous pouvez migrer vers des disques gérés dans les cas de figure suivants :

| Migrer...                                            | Lien vers la documentation                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Des machines virtuelles d’un groupe à haute disponibilité qui utilisent des disques non gérés vers des disques gérés   | [Convert VMs in an availability set to use managed disks](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set) (Convertir des machines virtuelles d’un groupe à haute disponibilité pour utiliser des disques gérés)                                                                        |
| Des disques non gérés Premium vers des disques gérés Premium   | [Convertir des machines virtuelles Azure existantes vers des disques gérés utilisant le même type de stockage](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type)                                                                         |
| Des disques non gérés Standard vers des disques gérés Standard | [Convertir des machines virtuelles Azure existantes vers des disques gérés utilisant le même type de stockage](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type)                                                                         |
| Des disques non gérés Standard vers des disques gérés Premium  | [Migrer des machines virtuelles Azure existantes utilisant des disques non gérés Standard vers des disques gérés Premium](virtual-machines-windows-convert-unmanaged-to-managed-disks.md#migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks)                            |
| Une machine virtuelle unique du modèle Classic vers le modèle Resource Manager sur des disques gérés     | [Migrer une machine virtuelle unique](virtual-machines-windows-migrate-single-classic-to-resource-manager.md)  | 
| Toutes les machines virtuelles d’un réseau virtuel du modèle Classic vers le modèle Resource Manager sur des disques gérés     | [Migration de ressources IaaS d’un environnement Classic vers Resource Manager](virtual-machines-windows-ps-migration-classic-resource-manager.md), puis [Convertir une machine virtuelle à partir de disques non gérés vers des disques gérés](virtual-machines-windows-convert-unmanaged-to-managed-disks.md) | 


## <a name="overview-of-managed-disks"></a>Vue d’ensemble des disques gérés

Les disques gérés du service Azure Managed Disks simplifient la gestion de votre stockage en éliminant la nécessité de gérer séparément les comptes de stockage.  Vous pouvez également migrer vos machines virtuelles Azure existantes vers des disques gérés afin de tirer parti de la fiabilité accrue des machines virtuelles dans un groupe à haute disponibilité. Cela permet de s’assurer que les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont suffisamment isolés les uns des autres pour éviter les points de défaillance uniques. Les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont automatiquement placés dans des unités d’échelle (tampons) de stockage distinctes, ce qui limite l’impact des défaillances d’unités d’échelle de stockage uniques dues à des défaillances matérielles et logicielles. Selon vos besoins, vous avez le choix entre deux types d’options de stockage : 
 
- Les [disques gérés Premium](../storage/storage-premium-storage.md) sont des supports de stockage basés sur des disques SSD (Solide State Drive) qui assurent de hautes performances et une faible latence pour les machines virtuelles dont les charges de travail nécessitent de nombreuses E/S. Vous pouvez tirer parti de la vitesse et des performances des disques gérés Premium en migrant vers ces disques.

- Les [disques gérés Standard](../storage/storage-standard-storage.md) utilisent un support de stockage basé sur un lecteur de disque dur (HDD) et sont mieux adaptés aux charges de travail de développement/test et d’accès peu fréquent, qui sont moins sensibles à la variabilité des performances. 


## <a name="plan-for-the-conversion-to-managed-disks"></a>Planification de la conversion en disques gérés

Cette section vous aide à prendre la meilleure décision concernant les types de machines virtuelles et de disques.


## <a name="location"></a>Emplacement

Choisissez un emplacement où Azure Managed Disks est disponible. Si vous effectuez une migration vers des disques gérés Premium, assurez-vous également que le stockage Premium est disponible dans la région où vous prévoyez la migration. Pour obtenir des informations à jour sur les emplacements disponibles, consultez [Services Azure par région](https://azure.microsoft.com/regions/#services).

## <a name="vm-sizes"></a>Tailles de machine virtuelle

Si vous effectuez une migration vers des disques gérés Premium, vous devez mettre à jour la taille de la machine virtuelle par rapport à la capacité de stockage Premium disponible dans la région où se trouve la machine virtuelle. Passez en revue les tailles de machine virtuelle compatibles avec le stockage Premium. Les spécifications des tailles des machines virtuelles Azure sont répertoriées dans la section [Tailles des machines virtuelles](virtual-machines-windows-sizes.md).
Passez en revue les caractéristiques de performances des machines virtuelles fonctionnant avec Premium Storage et choisissez la taille de machine virtuelle la mieux adaptée à votre charge de travail. Assurez-vous que la bande passante disponible est suffisante sur votre machine virtuelle pour gérer le trafic du disque.

## <a name="disk-sizes"></a>Tailles du disque

**Disques gérés Premium**

Il existe trois types de disques gérés Premium qui peuvent être utilisés avec votre machine virtuelle, chacun d’eux présentant des limites d’E/S par seconde et de débit spécifiques. Prenez en compte ces limites lors de la sélection du type de disque Premium pour votre machine virtuelle en fonction des besoins en capacité, en performances, en extensibilité et en charges maximales de votre application.

| Type de disque Premium  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Taille du disque           | 128 Go            | 512 Go            | 1024 Go (1 To)    |
| IOPS par disque       | 500               | 2 300              | 5 000              |
| Débit par disque | 100 Mo par seconde | 150 Mo par seconde | 200 Mo par seconde |

**Disques gérés Standard**

Il existe cinq types de disques gérés Standard qui peuvent être utilisés avec votre machine virtuelle. Chacun d’eux dispose d’une capacité différente, mais ils partagent les mêmes limites d’E/S par seconde et de débit. Choisissez le type de disque géré Standard selon les besoins en capacité de votre application.

| Type de disque Standard  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Taille du disque           | 30 Go            | 64 Go            | 128 Go           | 512 Go           | 1024 Go (1 To)   |
| IOPS par disque       | 500              | 500              | 500              | 500              | 500              |
| Débit par disque | 60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde |

## <a name="disk-caching-policy"></a>Stratégie de mise en cache du disque 

**Disques gérés Premium**

Par défaut, la stratégie de mise en cache est *Lecture seule* pour tous les disques de données Premium et *Lecture-écriture* pour le disque du système d’exploitation Premium attaché à la machine virtuelle. Ce paramètre de configuration est recommandé pour optimiser les performances des E/S de votre application. Pour les disques de données en écriture seule ou avec d'importantes opérations d'écriture (par ex., les fichiers journaux de SQL Server), désactivez la mise en cache du disque pour de meilleures performances de l'application.

## <a name="pricing"></a>Tarification

Consultez la [tarification des disques gérés](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). La tarification des disques gérés Premium est identique à celle des disques non gérés Premium. En revanche, la tarification des disques gérés Standard est différente de celle des disques non gérés Standard.



## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [disques gérés](../storage/storage-managed-disks-overview.md)



<!--HONumber=Feb17_HO2-->


