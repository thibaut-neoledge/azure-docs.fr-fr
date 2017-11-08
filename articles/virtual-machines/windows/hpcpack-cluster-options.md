---
title: Options de cluster HPC Pack Windows dans Azure | Microsoft Docs
description: "Découvrez les options de Microsoft HPC Pack pour créer et gérer un cluster HPC (High Performance Computing) Windows dans le cloud Azure"
services: virtual-machines-windows,cloud-services,batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 10/26/2017
ms.author: danlep
ms.openlocfilehash: 3dd00d4fb0d334e836256e66207358c034fc005a
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2017
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>Options de HPC Pack pour créer et gérer un cluster pour des charges de travail HPC Windows dans Azure
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Cet article traite des options de création de clusters HPC Pack pour exécuter des charges de travail Windows. Il existe également des options de création de clusters HPC Pack pour l’exécution de [charges de travail HPC Linux](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Cluster HPC Pack dans les machines virtuelles Azure et les groupes de machines virtuelles identiques
### <a name="azure-templates"></a>Modèles Azure
* (GitHub) [Modèles de cluster HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [Modèles de cluster HPC Pack 2012 R2](https://github.com/MsHpcPack/HPCPack2012R2)
* (Marketplace) [Cluster HPC Pack pour les charges de travail Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* (Marketplace) [Cluster HPC Pack pour les charges de travail Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* (Démarrage rapide) [Création d’un cluster HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (Démarrage rapide) [Création d’un cluster HPC avec une image de nœud de calcul personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Images d’ordinateur virtuel Azure
* [Nœud principal de HPC Pack 2016 sur Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2016?tab=Overview)
* [Nœud de calcul de HPC Pack 2016 sur Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2016?tab=Overview)
* [Nœud principal de HPC Pack 2016 sur Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016HeadNodeonWindowsServer2012R2?tab=Overview)
* [Nœud de calcul de HPC Pack 2016 sur Windows Server 2012 R2](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.HPCPack2016ComputeNodeonWindowsServer2012R2?tab=Overview)
* [Nœud principal de HPC Pack 2012 R2 sur Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Nœud de calcul de HPC Pack 2012 R2 sur Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [Nœud de calcul de HPC Pack avec Excel sur Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script-for-hpc-pack-2012-r2"></a>Script de déploiement PowerShell pour HPC Pack 2012 R2
* [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>Didacticiels
* [Didacticiel : déployer un cluster HPC Pack 2016 dans Azure](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Didacticiel : prise en main d’un cluster HPC Pack dans Azure pour exécuter des charges de travail Excel et SOA](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>Déploiement manuel avec le portail Azure
* [Configurer le nœud principal d’un cluster HPC Pack dans une machine virtuelle Azure](hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>Gestion de cluster
* [Gérer des nœuds de calcul dans un cluster HPC Pack dans Azure](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Agrandir et réduire les ressources de calcul Azure dans un cluster HPC Pack](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Envoyer des travaux à un cluster HPC Pack dans Azure](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Gestion des travaux dans HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)
* [Gérer un cluster HPC Pack 2016 dans Azure avec Azure Active Directory](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-worker-role-nodes"></a>Accroître les capacités avec des nœuds de rôle de travail 
* [Intégration à des instances de travail Azure avec HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Didacticiel : configurer un cluster hybride avec HPC Pack dans Azure](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [Ajouter des nœuds « en rafale » Azure à un nœud principal HPC Pack dans Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="burst-with-azure-batch"></a>Accroître les capacités avec Azure Batch
* [Intégration à Azure Batch avec HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="rdma-clusters-for-mpi-workloads"></a>Clusters RDMA pour charges de travail MPI
* [Configuration d’un cluster Windows RDMA avec HPC Pack pour exécuter des applications MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

