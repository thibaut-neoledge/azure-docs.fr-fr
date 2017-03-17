---
title: "À propos des disques et des disques durs virtuels pour les machines virtuelles Microsoft Azure Linux | Microsoft Docs"
description: "Découvrez les notions de base relatives aux disques et aux VHD pour les machines virtuelles Linux dans Azure."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7be8dd52-98f7-4187-9b78-55197915bc9b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: e34a2bfbf5f1ae544a729c994d91c485d48bb440
ms.openlocfilehash: 7cd157cd1c5730443a710ac46d923319789aec61
ms.lasthandoff: 03/01/2017


---
# <a name="about-disks-and-vhds-for-azure-linux-vms"></a>À propos des disques et des disques durs virtuels pour les machines virtuelles Azure Linux
Comme tout autre ordinateur, les machines virtuelles dans Azure utilisent des disques comme emplacement de stockage pour un système d’exploitation, des applications et des données. Toutes les machines virtuelles Azure possèdent au moins deux disques : un disque de système d’exploitation Linux et un disque temporaire. Le disque de système d’exploitation est créé à partir d’une image. Le disque de système d’exploitation et l’image sont en fait des disques durs virtuels (VHD) stockés dans un compte de stockage Azure. Les machines virtuelles peuvent également disposer d’un ou plusieurs disques de données, également stockés sur les VHD. 

Dans cet article, nous parlons des différentes utilisations pour les disques, puis nous abordons les types de disques que vous pouvez créer et utiliser. Cet article est également disponible pour les [machines virtuelles Windows](storage-about-disks-and-vhds-windows.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Disques utilisés par les machines virtuelles

Examinons comment les disques sont utilisés par les machines virtuelles.

## <a name="operating-system-disk"></a>Disque de système d’exploitation
Chaque machine virtuelle dispose d’un disque de système d’exploitation attaché. Il est enregistré comme disque SATA et porte le nom /dev/sda par défaut. Ce disque a une capacité maximale de 1 023 gigaoctets. 

## <a name="temporary-disk"></a>Disque temporaire
Chaque machine virtuelle contient un disque temporaire. Il fournit un stockage à court terme pour les applications et les processus, et est destiné à stocker seulement des données comme les fichiers de pagination ou d’échange. Les données présentes sur le disque temporaire peuvent être perdues lors d’un [événement de maintenance](../virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-planned-vs-unplanned-maintenance) ou quand vous [redéployez une machine virtuelle](../virtual-machines/virtual-machines-linux-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Lors d’un redémarrage standard de la machine virtuelle, les données présentes sur le disque temporaire doivent normalement être conservées.

Sur les machines virtuelles Linux, le disque se nomme généralement **/dev/sdb**, et est formaté et monté sur **/mnt** par l’agent Linux Azure. La taille du disque temporaire varie en fonction de la taille de la machine virtuelle. Pour plus d’informations, consultez [Taille des machines virtuelles Linux](../virtual-machines/virtual-machines-linux-sizes.md).

Pour plus d’informations sur l’utilisation du disque temporaire par Azure, voir [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disque de données
Un disque de données est un VHD attaché à une machine virtuelle pour stocker des données d’application ou d’autres données que vous souhaitez conserver. Les disques de données sont enregistrés en tant que disques SCSI et sont nommés avec la lettre de votre choix. Chaque disque de données possède une capacité maximale de 1 023 gigaoctets. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher et le type de stockage que vous pouvez utiliser pour héberger les disques.

> [!NOTE]
> Pour plus d’informations sur les capacités des machines virtuelles, consultez [Taille des machines virtuelles Linux](../virtual-machines/virtual-machines-linux-sizes.md).
> 

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée un disque de système d’exploitation. Si vous utilisez une image incluant des disques de données, Azure crée également ces derniers lors de la création de la machine virtuelle. Vous pouvez également ajouter des disques de données après avoir créé la machine virtuelle.

Vous pouvez ajouter un disque de données à une machine virtuelle à tout moment, **en attachant** le disque à la machine virtuelle. Vous pouvez utiliser un VHD que vous avez téléchargé ou copié sur votre compte de stockage ou qui a été créé par Azure pour vous. Le fait d’attacher un disque de données associe le fichier VHD à la machine virtuelle en plaçant un « bail » sur le VHD afin qu’il ne puisse pas être supprimé du stockage tant qu’il est attaché.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Résolution des problèmes
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Attacher un disque](../virtual-machines/virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour ajouter un stockage supplémentaire pour votre machine virtuelle.
* [Configurer un RAID logiciel](../virtual-machines/virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour la redondance.
* [Capturer une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) afin de déployer rapidement des machines virtuelles supplémentaires.


