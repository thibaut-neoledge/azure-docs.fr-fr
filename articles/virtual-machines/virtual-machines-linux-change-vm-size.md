---
title: Redimensionnement d’une machine virtuelle Linux | Microsoft Docs
description: Comment augmenter ou diminuer les capacités d’une machine virtuelle Linux, en en modifiant la taille.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: ''
tags: ''

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mikewasson

---
# Comment redimensionner une machine virtuelle Linux
## Vue d'ensemble
Une fois que vous configurez une machine virtuelle, vous pouvez mettre la machine virtuelle à l’échelle en [en modifiant la taille][vm-sizes]. Dans certains cas, vous devez commencer par libérer la machine virtuelle. Cela peut se produire si la nouvelle taille n’est pas disponible sur le cluster matériel qui héberge la machine virtuelle.

Cet article montre comment redimensionner une machine virtuelle Linux à l’aide de l’[interface de ligne de commande Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

le modèle de déploiement classique.

## Redimensionner une machine virtuelle Linux
Pour redimensionner une machine virtuelle, procédez comme suit.

1. Exécutez la commande d’interface de ligne de commande ci-dessous. Cette commande répertorie les tailles de machines virtuelles qui sont disponibles sur le cluster matériel sur lequel la machine virtuelle est hébergée.
   
    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```
2. Si la taille voulue n’est pas répertoriée, exécutez la commande suivante pour redimensionner la machine virtuelle.
   
    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```
   
    La machine virtuelle redémarre pendant le processus. Après le redémarrage, votre système d’exploitation existant et les disques de données sont remappés. Tout le contenu du disque temporaire est perdu.
   
    Utilisez l’option `--enable-boot-diagnostics` pour activer la consignation des erreurs de démarrage dans [Diagnostics de démarrage][boot-diagnostics].
3. Sinon, si la taille souhaitée n’est pas répertoriée, exécutez les commandes suivantes pour libérer la machine virtuelle et la redimensionner, puis redémarrez-la.
   
    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```
   
   > [!WARNING]
   > Le fait de libérer la machine virtuelle libère également toutes les adresses IP dynamiques affectées à la machine virtuelle. Les disques de données et du système d’exploitation ne sont pas affectés.
   > 
   > 

## Étapes suivantes
Pour une évolutivité supplémentaire, exécutez plusieurs instances de machine virtuelle et augmentez leur taille. Pour plus d’informations, consultez [Mise à l’échelle automatique des machines Linux dans un groupe de machines virtuelles à échelle identique][scale-set].

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md
[vm-sizes]: virtual-machines-linux-sizes.md

<!---HONumber=AcomDC_0824_2016-->