---
title: "À propos des machines virtuelles nécessitant beaucoup de ressources système avec Windows | Microsoft Docs"
description: "Consultez les informations et les considérations générales relatives à l’utilisation de la série H Azure et des tailles Azure nécessitant beaucoup de ressources système A8, A9, A10 et A11 pour des machines virtuelles et services cloud Windows."
services: virtual-machines-windows, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7f86fb57d9082e19b506c42737aaa020d474d599
ms.lasthandoff: 04/27/2017


---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-windows"></a>About H-series and compute-intensive A-series VMs for Linux (À propos des machines virtuelles de série H ou de calcul intensif de série A pour Windows)
Cet article fournit des informations et quelques considérations générales sur l’utilisation de la nouvelle série H Azure et des instances A8, A9, A10 et A11 antérieures, également appelées instances *nécessitant beaucoup de ressources système* . Cet article se concentre sur l’utilisation de ces instances pour les machines virtuelles Windows. Vous pouvez également les utiliser pour les [machines virtuelles Linux](../linux/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour plus d’informations sur les spécifications de base, les capacités de stockage et les disques, consultez l’article [Tailles de machines virtuelles](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accès au réseau RDMA
Pour accéder au réseau RDMA Azure pour le trafic MPI Windows, les instances prenant en charge RDMA doivent remplir les conditions suivantes : 

* **Système d’exploitation**
  
  * **Machines virtuelles** : Windows Server 2012 R2, Windows Server 2012
  * **Services cloud** : famille de systèmes d’exploitation invités Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2

    > [!NOTE]
    > Actuellement, Windows Server 2016 ne prend pas en charge la connectivité RDMA dans Azure.
    >
    
* **MPI** : Microsoft MPI (MS-MPI) 2012 R2 ou version ultérieure, bibliothèque Intel MPI 5.x

  Les implémentations MPI prises en charge utilisent l’interface Microsoft Network Direct pour la communication entre les instances. 
* **Extension de machine virtuelle HpcVmDrivers** : sur des machines virtuelles prenant en charge RDMA, l’extension HpcVmDrivers doit être ajoutée pour installer les pilotes d’appareils réseau Windows nécessaires à la connectivité RDMA. (Dans certains déploiements des instances A8 et A9, l’extension HpcVmDrivers est ajoutée automatiquement.) Si vous devez ajouter l’extension de machine virtuelle sur une machine virtuelle, vous pouvez utiliser les applets de commande [Azure PowerShell](/powershell/azure/overview). 

  
  Par exemple, pour installer la dernière version 1.1 de l’extension HpcVMDrivers sur une machine virtuelle existante prenant en charge RDMA, nommée myVM et déployée dans le modèle de déploiement Resource Manager :

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Pour plus d’informations, consultez [Extensions et fonctionnalités de la machine virtuelle](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vous pouvez également utiliser les extensions pour les machines virtuelles déployées dans le [modèle de déploiement classique](classic/manage-extensions.md).


## <a name="considerations-for-hpc-pack-and-windows"></a>Considérations relatives à HPC Pack et Windows
[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solution gratuite de cluster et de gestion des travaux HPC de Microsoft, n’est pas requis pour utiliser les instances nécessitant beaucoup de ressources système avec Windows Server. Il s’agit cependant d’une option permettant de créer un cluster de calcul dans Azure pour exécuter des applications MPI basées sur Windows et d’autres charges de travail HPC. HPC Pack 2012 R2 et les versions ultérieures incluent un environnement d’exécution pour MS-MPI, qui peut utiliser le réseau RDMA Azure en cas de déploiement sur des machines virtuelles prenant en charge RDMA.

Pour plus d’informations et pour obtenir les listes de vérification afin d’utiliser les instances nécessitant beaucoup de ressources système avec HPC Pack sur Windows Server, consultez [Configuration d’un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la disponibilité et la tarification des tailles nécessitant beaucoup de ressources système, consultez [Tarification des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) et [Tarification Services cloud](https://azure.microsoft.com/pricing/details/cloud-services/).
* Pour plus d’informations sur les capacités de stockage et sur les disques, consultez [Tailles de machines virtuelles](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour commencer à déployer et à utiliser des instances nécessitant beaucoup de ressources système avec HPC Pack sur Windows, consultez [Configuration d’un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Pour plus d’informations sur l’utilisation d’instances nécessitant beaucoup de ressources système afin d’exécuter des applications MPI avec Azure Batch, consultez [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../../batch/batch-mpi.md).


