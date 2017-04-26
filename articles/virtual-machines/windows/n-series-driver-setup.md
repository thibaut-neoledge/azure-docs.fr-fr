---
title: "Configuration du pilote série N Azure pour Windows | Microsoft Docs"
description: "Procédure de configuration des pilotes GPU NVIDIA pour les machines virtuelles série N exécutant Windows dans Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 1767a2db05abd3abadfedbef86c38e55c5a57980
ms.lasthandoff: 04/04/2017


---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Configuration des pilotes GPU NVIDIA pour les machines virtuelles série N exécutant Windows Server
Pour tirer parti des fonctionnalités GPU des machines virtuelles série N Azure exécutant Windows Server 2016 ou Windows Server 2012 R2, vous devez installer des pilotes graphiques NVIDIA sur chaque machine virtuelle après le déploiement. Des informations de configuration du pilote sont également disponibles pour [les machines virtuelles Linux](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour plus d’informations sur les spécifications de base, les capacités de stockage et les disques, consultez l’article [Tailles de machines virtuelles](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Consultez aussi [Considérations générales pour les machines virtuelles de série N](#general-considerations-for-n-series-vms).



## <a name="supported-gpu-drivers"></a>Pilotes GPU pris en charge

Connectez-vous à chaque machine virtuelle série N à l’aide du Bureau à distance. Téléchargez, extrayez et installez le pilote pris en charge pour votre système d’exploitation Windows. 

### <a name="nvidia-tesla-drivers-for-nc-vms-tesla-k80"></a>Pilotes Tesla NVIDIA pour les machines virtuelles NC (Tesla K80)



| SE | Version du pilote |
| -------- |------------- |
| Windows Server 2016 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2016-international-whql.exe) (.exe) |
| Windows Server 2012 R2 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2008-2012r2-64bit-international-whql.exe) (.exe) |

> [!NOTE]
> Les liens de téléchargement de pilotes Tesla fournis ici sont à jour au moment de la publication. Pour les pilotes les plus récents, visitez le site Web de [NVIDIA](http://www.nvidia.com/).
>

### <a name="nvidia-grid-drivers-for-nv-vms-tesla-m60"></a>Pilotes GRID NVIDIA pour les machines virtuelles NV (Tesla M60)

| SE | Version du pilote |
| -------- |------------- |
| Windows Server 2016 | [369.95](https://go.microsoft.com/fwlink/?linkid=836843) (.zip) |
| Windows Server 2012 R2 | [369.95](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)  |



## <a name="verify-gpu-driver-installation"></a>Vérification de l’installation du pilote du GPU

Sur les machines virtuelles NV Azure, le redémarrage est nécessaire après l’installation du pilote. Sur les machines virtuelles NC, le redémarrage n’est pas nécessaire.

Vous pouvez vérifier l’installation du pilote dans le Gestionnaire de périphériques. L’exemple suivant illustre une configuration réussie de la carte Tesla K80 sur une machine virtuelle NC Azure.

![Propriétés du pilote GPU](./media/n-series-driver-setup/GPU_driver_properties.png)

Pour interroger l’état de l’appareil GPU, exécutez l’utilitaire de ligne de commande [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) installé avec le pilote. 

![État de l’appareil NVIDIA](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>Réseau RDMA pour les machines virtuelles NC24r

La connectivité réseau RDMA peut être activée sur des machines virtuelles NC24r déployées dans le même groupe à haute disponibilité. L’extension HpcVmDrivers doit être ajoutée pour installer les pilotes d’appareils réseau Windows nécessaires à la connectivité RDMA. Pour ajouter l’extension de machine virtuelle sur une machine virtuelle NC24r, utilisez les applets de commande [Azure PowerShell](/powershell/azureps-cmdlets-docs) pour Azure Resource Manager.

> [!NOTE]
> Actuellement, seul Windows Server 2012 R2 prend en charge le réseau RDMA sur des machines virtuelles NC24r.
> 

Pour installer la dernière version 1.1 de l’extension HpcVMDrivers sur une machine virtuelle existante prenant en charge RDMA et nommée myVM dans la région États-Unis de l'Ouest :
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  Pour plus d’informations, consultez [Extensions et fonctionnalités de machine virtuelle pour Windows](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Le réseau RDMA prend en charge le trafic MPI (Message Passing Interface) pour les applications exécutées avec [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) ou Intel MPI 5.x. 

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les GPU NVIDIA sur les machines virtuelles série N, consultez :
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (pour les machines virtuelles NC Azure)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (pour les machines virtuelles NV Azure)

* Les développeurs qui créent des applications avec accélération GPU pour les GPU Tesla NVIDIA peuvent également télécharger et installer le kit d’outils 8 CUDA pour [Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) ou [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe). Pour plus d’informations, consultez le [Guide d’installation de CUDA](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).



