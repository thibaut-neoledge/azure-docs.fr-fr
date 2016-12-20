---
title: "Attacher un disque de données à une machine virtuelle Linux | Microsoft Docs"
description: "Découvrez comment attacher un disque de données nouveau ou existant à une machine virtuelle Linux dans le portail Azure à l’aide du modèle de déploiement Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 743f6abb871bdc4f65f302f0c1efdc4603c52626


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Attachement d’un disque de données à une machine virtuelle Linux dans le portail Azure
Cet article vous explique comment attacher des disques nouveaux et existants à une machine virtuelle Linux par le biais du portail Azure. Vous pouvez également [attacher un disque de données à une machine virtuelle Windows dans le Portail Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Avant cela, passez en revue les conseils suivants :

* La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour utiliser le stockage de Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques de comptes de stockage Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md).
* Les disques attachés aux machines virtuelles sont en fait des fichiers .vhd dans un compte de stockage Azure. Pour en savoir plus, voir la section [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour un nouveau disque, vous n’avez pas besoin de le créer au préalable, car Azure le crée lorsque vous l’attachez.
* Pour un disque existant, le fichier .vhd doit être disponible dans un compte de stockage Azure. Vous pouvez utiliser un fichier .vhd présent, s’il n’est attaché à aucune autre machine virtuelle, ou charger votre propre fichier .vhd sur le compte de stockage.

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois le disque ajouté, vous devez le préparer pour utilisation. Consultez « Initialisation d’un nouveau disque de données dans Linux » dans cet [article](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux) pour le système d’exploitation de la machine virtuelle.



<!--HONumber=Nov16_HO3-->


