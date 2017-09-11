---
title: Options de cluster HPC Pack Linux dans Azure | Microsoft Docs
description: "Découvrez les options de Microsoft HPC Pack pour créer et gérer un cluster HPC (High Performance Computing) Linux dans le cloud Azure"
services: virtual-machines-linux,cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 08/25/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 99aee6b73242d21842b02ded8c9e4e25bc5cedb4
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>Options de HPC Pack pour créer et gérer un cluster pour des charges de travail HPC Linux dans Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Cet article traite des options d’utilisation de HPC Pack pour exécuter des charges de travail Linux. Il existe également des options d’exécution [de charges de travail Windows HPC avec HPC Pack](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="hpc-pack-cluster-in-azure-vms"></a>Cluster HPC Pack dans les machines virtuelles Azure
### <a name="azure-templates"></a>Modèles Azure
* (GitHub) [Modèles de cluster HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016)
* (Place de marché) [Cluster HPC Pack pour les charges de travail Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (Démarrage rapide) [Créer un cluster HPC avec des nœuds de calcul Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>Script de déploiement PowerShell pour HPC Pack 2012 R2
* [Créer un cluster HPC Linux avec le script de déploiement HPC Pack IaaS](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Didacticiels
* [Didacticiel : Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Didacticiel : Exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Didacticiel : Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Didacticiel : Exécuter STAR-CCM+ avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>Gestion de cluster
* [Envoyer des travaux à un cluster HPC Pack dans Azure](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gestion des travaux dans HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>Clusters RDMA pour charges de travail MPI
* [Didacticiel : Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)


