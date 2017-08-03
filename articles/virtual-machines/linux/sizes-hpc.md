---
title: Tailles des machines virtuelles Linux Azure - Calcul haute performance | Microsoft Docs
description: "Répertorie les différentes tailles disponibles pour les machines virtuelles de calcul haute performance Linux dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 4962c26e6bf2ed36ce670cc78f4ecfcab871a8bf
ms.contentlocale: fr-fr
ms.lasthandoff: 07/14/2017

---

# <a name="high-performance-compute-linux-vm-sizes"></a>Tailles de machine virtuelle Linux pour calcul haute performance

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>Instances prenant en charge RDMA
Un sous-ensemble d’instances nécessitant beaucoup de ressources système (H16r, H16mr, A8 et A9) offre une interface réseau pour la connectivité par accès direct à la mémoire à distance (RDMA). Cette interface s’ajoute à l’interface réseau Azure standard disponible pour d’autres tailles de machine virtuelle. 
  
Cette interface permet aux instances prenant en charge RDMA de communiquer sur un réseau InfiniBand, opérant à des vitesses FDR pour les machines virtuelles H16r et H16mr, et à des vitesses QDR pour les machines virtuelles A8 et A9. Ces fonctionnalités RDMA peuvent améliorer l’extensibilité et les performances des applications MPI (Message Passing Interface).

Voici la configuration requise pour que les machines virtuelles Linux prenant en charge RDMA puissent accéder au réseau RDMA Azure :
 
* **Distributions** : vous devez déployer des machines virtuelles à partir d’images SUSE Linux Enterprise Server (SLES) ou Rogue Wave Software (OpenLogic précédemment) prenant en charge RDMA ou d’images HPC basées sur OpenLogic CentOS dans la Place de marché Azure. Les images de la Place de marché suivantes prennent en charge la connectivité RDMA :
  
    * SLES 12 SP1 pour HPC ou SLES 12 SP1 pour HPC (Premium)
    
    * HP basé sur CentOS 7.3, HP basé sur CentOS 7.1, HP basé sur CentOS 6.8 ou HP basé sur CentOS 6.5  
 
        > [!NOTE]
        > Pour les machines virtuelles de la série H, nous vous recommandons une image SLES 12 SP1 pour HPC ou une image HPC basée sur CentOS 7.1 ou version ultérieure.
        >
        > Sur les images HPC basées sur CentOS, les mises à jour du noyau sont désactivées dans le fichier de configuration **yum** . Cela s’explique par le fait que les pilotes RDMA Linux sont distribués sous forme de package RPM, et que les mises à jour du pilote peuvent ne pas fonctionner si le noyau est mis à jour.
        > 
        > 
* **MPI** - Intel MPI Library 5.x
  
    En fonction de l’image de la Place de marché choisie, une gestion de licence, une installation ou une configuration distinctes d’Intel MPI peuvent être nécessaires, comme suit : 
  
  * **Image SLES 12 SP1 pour HPC** : les packages Intel MPI sont distribués sur la machine virtuelle. Installez en exécutant la commande suivante :

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **Images HPC basées sur CentOS** : Intel MPI 5.1 est déjà installé.  
    
    Une configuration supplémentaire du système est nécessaire pour exécuter des travaux MPI sur des machines virtuelles en cluster. Par exemple, sur un cluster de machines virtuelles, vous devez établir une approbation entre les nœuds de calcul. Pour les paramètres classiques, consultez [Configurer un cluster RDMA Linux pour exécuter des applications MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Considérations sur la topologie réseau
* Sur des machines virtuelles Linux compatibles RDMA dans Azure, Eth1 est réservé au trafic réseau RDMA. Ne modifiez aucun paramètre Eth1 ou des informations du fichier de configuration faisant référence à ce réseau. Eth0 est réservé au trafic réseau Azure normal.

* Dans Azure, IP over Infiniband (IB) n’est pas pris en charge. Seul RDMA over IB est pris en charge.

## <a name="using-hpc-pack"></a>Utilisation de HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solution gratuite de gestion des travaux et des clusters HPC de Microsoft, vous offre un moyen d’utiliser les instances nécessitant beaucoup de ressources système avec Linux. Les dernières versions de HPC Pack prennent en charge l’exécution de plusieurs distributions Linux sur les nœuds de calcul déployés dans les machines virtuelles Azure et gérés par un nœud principal Windows Server. Avec des nœuds de calcul Linux prenant en charge RDMA exécutant Intel MPI, HPC Pack peut planifier et exécuter des applications MPI Linux qui accèdent au réseau RDMA. Consultez [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Étapes suivantes

- Pour commencer à déployer et à utiliser des tailles nécessitant beaucoup de ressources système avec RDMA sur Linux, consultez [Configurer un cluster RDMA Linux pour exécuter des applications MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.





