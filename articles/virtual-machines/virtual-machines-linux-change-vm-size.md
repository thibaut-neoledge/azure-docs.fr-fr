---
title: "Redimensionnement d’une machine virtuelle Linux | Microsoft Docs"
description: "Comment augmenter ou diminuer les capacités d’une machine virtuelle Linux, en en modifiant la taille."
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mikewasson
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: 0032c330288ea37508c919b50a41a5e58fc28e90


---
# <a name="how-to-resize-a-linux-vm"></a>Comment redimensionner une machine virtuelle Linux
## <a name="overview"></a>Vue d'ensemble
Une fois que vous avez approvisionné une machine virtuelle, vous pouvez la mettre à l’échelle en modifiant sa [taille][vm-sizes]. Dans certains cas, vous devez commencer par libérer la machine virtuelle. Cela peut se produire si la nouvelle taille n’est pas disponible sur le cluster matériel qui héberge la machine virtuelle.

Cet article montre comment redimensionner une machine virtuelle Linux à l’aide de [l’interface de ligne de commande Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="resize-a-linux-vm"></a>Redimensionner une machine virtuelle Linux
Pour redimensionner une machine virtuelle, procédez comme suit.

1. Exécutez la commande d’interface de ligne de commande ci-dessous. Cette commande répertorie les tailles de machines virtuelles qui sont disponibles sur le cluster matériel sur lequel la machine virtuelle est hébergée.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Si la taille voulue n’est pas répertoriée, exécutez la commande suivante pour redimensionner la machine virtuelle.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    La machine virtuelle redémarre pendant le processus. Après le redémarrage, votre système d’exploitation existant et les disques de données sont remappés. Tout le contenu du disque temporaire est perdu.
   
    Utilisez l’option `--enable-boot-diagnostics` pour activer la consignation des erreurs de démarrage dans [Diagnostics de démarrage][boot-diagnostics].
3. Sinon, si la taille souhaitée n’est pas répertoriée, exécutez les commandes suivantes pour libérer la machine virtuelle et la redimensionner, puis redémarrez-la.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > Le fait de libérer la machine virtuelle libère également toutes les adresses IP dynamiques affectées à la machine virtuelle. Les disques de données et du système d’exploitation ne sont pas affectés.
   > 
   > 

## <a name="next-steps"></a>Étapes suivantes
Pour une évolutivité supplémentaire, exécutez plusieurs instances de machine virtuelle et augmentez leur taille. Pour plus d’informations, consultez [Mise à l’échelle automatique des machines Linux dans un groupe de machines virtuelles identiques][scale-set]. 

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md



<!--HONumber=Nov16_HO3-->


