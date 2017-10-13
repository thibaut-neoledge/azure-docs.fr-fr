---
title: "Détacher un disque de données d’une machine virtuelle Linux - Azure | Microsoft Docs"
description: "Apprenez à détacher un disque de données à partir d’une machine virtuelle dans Azure à l’aide de CLI 2.0 ou du portail Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: 3f29547e1da6028b1e4b91d9e29fd3bcdfe08d50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Comment détacher un disque de données d’une machine virtuelle Linux

Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage. 

> [!WARNING]
> Si vous détachez un disque, il n’est pas supprimé automatiquement. Si vous êtes abonné au stockage Premium, vous continuerez à engager des frais de stockage pour le disque. Pour plus d’informations, consultez [Tarification et facturation de Premium Storage](../../storage/common/storage-premium-storage.md#pricing-and-billing). 
> 
> 

Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.  

## <a name="detach-a-data-disk-using-cli-20"></a>Détacher un disque de données à l’aide de CLI 2.0

```azurecli
az vm disk detach -g myResourceGroup --vm-name myVm -n myDataDisk
```

Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.


## <a name="detach-a-data-disk-using-the-portal"></a>Détacher un disque de données avec le portail
1. Dans le concentrateur du portail, sélectionnez **Machines virtuelles**.
2. Sélectionnez la machine virtuelle qui possède le disque de données que vous souhaitez détacher, puis cliquez sur **Arrêter** pour libérer la machine virtuelle.
3. Dans le panneau de la machine virtuelle, sélectionnez **Disques**.
4. En haut du panneau **Disques**, sélectionnez **Modifier**.
5. Dans le panneau **Disques**, à l’extrême droite du disque de données que vous souhaitez détacher, cliquez sur le bouton détacher ![image du bouton détacher](./media/detach-disk/detach.png).
5. Une fois que le disque a été supprimé, cliquez sur Enregistrer en haut du panneau.
6. Dans le panneau de la machine virtuelle, cliquez sur **Présentation**, puis cliquez sur le bouton **Démarrer** en haut du panneau pour redémarrer la machine virtuelle.

Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.








## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez réutiliser le disque de données, vous pouvez simplement l’[attacher à une autre machine virtuelle](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

