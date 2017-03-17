---
title: "À propos des disques et des VHD pour les machines virtuelles Microsoft Azure Windows | Microsoft Docs"
description: "Découvrez les notions de base relatives aux disques et aux VHD pour les machines virtuelles Windows dans Azure."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 0142c64d-5e8c-4d62-aa6f-06d6261f485a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: e34a2bfbf5f1ae544a729c994d91c485d48bb440
ms.openlocfilehash: 1027f9c73a45b7d148643f5ec217683f71c4e899
ms.lasthandoff: 03/01/2017


---
# <a name="about-disks-and-vhds-for-azure-windows-vms"></a>À propos des disques et des VHD pour les machines virtuelles Azure Windows
Comme tout autre ordinateur, les machines virtuelles dans Azure utilisent des disques comme emplacement de stockage pour un système d’exploitation, des applications et des données. Toutes les machines virtuelles Azure possèdent au moins deux disques : un disque de système d’exploitation Windows et un disque temporaire. Le disque de système d’exploitation est créé à partir d’une image. Le disque de système d’exploitation et l’image sont des disques durs virtuels (VHD) stockés dans un compte de stockage Azure. Les machines virtuelles peuvent également disposer d’un ou plusieurs disques de données, également stockés sur les VHD. 

Dans cet article, nous parlons des différentes utilisations pour les disques, puis nous abordons les types de disques que vous pouvez créer et utiliser. Cet article est également disponible pour les [machines virtuelles Linux](storage-about-disks-and-vhds-linux.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Disques utilisés par les machines virtuelles

Examinons comment les disques sont utilisés par les machines virtuelles.

### <a name="operating-system-disk"></a>Disque de système d’exploitation
Chaque machine virtuelle dispose d’un disque de système d’exploitation attaché. Il est enregistré comme disque SATA et porte le nom de lecteur C: par défaut. Ce disque a une capacité maximale de 1 023 gigaoctets. 

### <a name="temporary-disk"></a>Disque temporaire
Chaque machine virtuelle contient un disque temporaire. Il fournit un stockage à court terme pour les applications et les processus, et est destiné à stocker seulement des données comme les fichiers de pagination ou d’échange. Les données présentes sur le disque temporaire peuvent être perdues lors d’un [événement de maintenance](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-planned-vs-unplanned-maintenance) ou quand vous [redéployez une machine virtuelle](../virtual-machines/virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Lors d’un redémarrage standard de la machine virtuelle, les données présentes sur le disque temporaire doivent normalement être conservées.

Le disque temporaire est étiqueté lecteur D: par défaut, et utilisé pour le stockage de pagefile.sys. Pour remapper ce disque à une autre lettre de lecteur, voir [Modification de la lettre de lecteur du disque temporaire Windows](../virtual-machines/virtual-machines-windows-classic-change-drive-letter.md). La taille du disque temporaire varie en fonction de la taille de la machine virtuelle. Pour plus d’informations, voir [Tailles des machines virtuelles Windows](../virtual-machines/virtual-machines-windows-sizes.md).

Pour plus d’informations sur l’utilisation du disque temporaire par Azure, voir [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>Disque de données
Un disque de données est un VHD attaché à une machine virtuelle pour stocker des données d’application ou d’autres données que vous souhaitez conserver. Les disques de données sont enregistrés en tant que disques SCSI et sont nommés avec la lettre de votre choix. Chaque disque de données possède une capacité maximale de 1 023 gigaoctets. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher et le type de stockage que vous pouvez utiliser pour héberger les disques.

> [!NOTE]
> Pour plus d’informations sur les capacités des machines virtuelles, consultez [Tailles des machines virtuelles Windows](../virtual-machines/virtual-machines-windows-sizes.md).
> 

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée un disque de système d’exploitation. Si vous utilisez une image incluant des disques de données, Azure crée également ces derniers lors de la création de la machine virtuelle. Vous pouvez également ajouter des disques de données après avoir créé la machine virtuelle.

Vous pouvez ajouter un disque de données à une machine virtuelle à tout moment, **en attachant** le disque à la machine virtuelle. Vous pouvez utiliser un VHD que vous avez téléchargé ou copié sur votre compte de stockage ou qui a été créé par Azure pour vous. Le fait d’attacher un disque de données associe le fichier VHD à la machine virtuelle en plaçant un « bail » sur le VHD afin qu’il ne puisse pas être supprimé du stockage tant qu’il est attaché.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Une dernière recommandation : utilisez TRIM avec des disques standard non gérés 

Si vous utilisez les disques standard non gérés (disque dur), vous devez activer TRIM. TRIM ignore les blocs inutilisés sur le disque afin que vous soyez facturé uniquement pour le stockage que vous utilisez réellement. Vous pouvez ainsi faire des économies si vous créez des fichiers volumineux, puis les supprimez. 

Vous pouvez exécuter cette commande pour vérifier le paramètre TRIM. Ouvrez une invite de commandes sur votre machine virtuelle Windows et saisissez :

```
fsutil behavior query DisableDeleteNotify
```

Si la commande renvoie 0, TRIM est bien activé. Si la valeur 1 est renvoyée, exécutez la commande suivante pour activer TRIM :

```
fsutil behavior set DisableDeleteNotify 0
```

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Étapes suivantes
* [Attacher un disque](../virtual-machines/virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour ajouter un stockage supplémentaire pour votre machine virtuelle.
* [Charger une image de machine virtuelle Windows dans Azure](../virtual-machines/virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à utiliser lors de la création d’une machine virtuelle.
* [Changer la lettre de lecteur du disque temporaire Windows](../virtual-machines/virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) pour que votre application puisse utiliser le lecteur D: pour les données.


