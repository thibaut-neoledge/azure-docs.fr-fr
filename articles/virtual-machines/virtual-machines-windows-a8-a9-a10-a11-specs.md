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
ms.date: 11/21/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 5a46b53f42fce7577485517e9d345cf2ad3f0926
ms.openlocfilehash: d4d0a221d531c4f6eda7bb6410fbc4dcebf5074b


---
# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>À propos des machines virtuelles de série H et de série A nécessitant beaucoup de ressources système
Cet article fournit des informations et quelques considérations générales sur l’utilisation de la nouvelle série H Azure et des instances A8, A9, A10 et A11 antérieures, également appelées instances *nécessitant beaucoup de ressources système* . Cet article se concentre sur l’utilisation de ces instances pour les machines virtuelles Windows. Il est également disponible pour les [machines virtuelles Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour plus d’informations sur les spécifications de base, les capacités de stockage et les disques, consultez l’article [Tailles de machines virtuelles](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Accès au réseau RDMA
Pour accéder au réseau RDMA Azure pour le trafic MPI Windows, les instances prenant en charge RDMA doivent remplir les conditions suivantes : 

* **Système d’exploitation**
  
  * **Machines virtuelles** : Windows Server 2012 R2, Windows Server 2012
  * **Services cloud** : famille de systèmes d’exploitation invités Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2
* **MPI** : Microsoft MPI (MS-MPI) 2012 R2 ou version ultérieure, bibliothèque Intel MPI 5.x

  Les implémentations MPI prises en charge utilisent l’interface Microsoft Network Direct pour la communication entre les instances. 
* **Extension de machine virtuelle HpcVmDrivers** : sur des machines virtuelles prenant en charge RDMA, l’extension HpcVmDrivers doit être ajoutée pour installer les pilotes d’appareils réseau Windows nécessaires à la connectivité RDMA. (Dans certains déploiements des instances A8 et A9, l’extension HpcVmDrivers est ajoutée automatiquement.) Si vous devez ajouter l’extension de machine virtuelle sur une machine virtuelle, vous pouvez utiliser les applets de commande [Azure PowerShell](/powershell/azureps-cmdlets-docs) pour Azure Resource Manager.

  Pour obtenir des informations sur la dernière extension HpcVmDrivers :

  ```PowerShell
  Get-AzureVMAvailableExtension -ExtensionName  "HpcVmDrivers"
  ```

  Pour installer la dernière version 1.1 de l’extension HpcVMDrivers sur une machine virtuelle existante prenant en charge RDMA et nommée myVM :
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Pour plus d’informations, consultez [Gérer les extensions de machine virtuelle](virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Vous pouvez également utiliser les extensions de machines virtuelles dans le [modèle de déploiement classique](virtual-machines-windows-classic-manage-extensions.md).


## <a name="considerations-for-hpc-pack-and-windows"></a>Considérations relatives à HPC Pack et Windows
[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solution gratuite de cluster et de gestion des travaux HPC de Microsoft, n’est pas requis pour utiliser les instances nécessitant beaucoup de ressources système avec Windows Server. Il s’agit cependant d’une option permettant de créer un cluster de calcul dans Azure pour exécuter des applications MPI basées sur Windows et d’autres charges de travail HPC. HPC Pack 2012 R2 et les versions ultérieures incluent un environnement d’exécution pour MS-MPI, qui peut utiliser le réseau RDMA Azure en cas de déploiement sur des machines virtuelles prenant en charge RDMA.

Pour plus d’informations et pour obtenir les listes de vérification afin d’utiliser les instances nécessitant beaucoup de ressources système avec HPC Pack sur Windows Server, consultez [Configuration d’un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la disponibilité et la tarification des tailles nécessitant beaucoup de ressources système, consultez [Tarification des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) et [Tarification Services cloud](https://azure.microsoft.com/pricing/details/cloud-services/).
* Pour plus d’informations sur les capacités de stockage et sur les disques, consultez [Tailles de machines virtuelles](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour commencer à déployer et à utiliser des instances nécessitant beaucoup de ressources système avec HPC Pack sur Windows, consultez [Configuration d’un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Pour plus d’informations sur l’utilisation d’instances nécessitant beaucoup de ressources système afin d’exécuter des applications MPI avec Azure Batch, consultez [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../batch/batch-mpi.md).




<!--HONumber=Jan17_HO1-->


