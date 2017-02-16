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
ms.date: 11/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 7c481cfae5f97b71c0ab184419ceaa46ab3f5a5b
ms.openlocfilehash: 394d82e444bdbc8b07243d92743ceb660f142509


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Attachement d’un disque de données à une machine virtuelle Linux dans le portail Azure
Cet article vous explique comment attacher des disques nouveaux et existants à une machine virtuelle Linux par le biais du portail Azure. Vous pouvez également [attacher un disque de données à une machine virtuelle Windows dans le Portail Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Avant cela, passez en revue les conseils suivants :

* La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour utiliser le stockage de Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques de comptes de stockage Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Les disques attachés aux machines virtuelles sont en fait des fichiers .vhd dans un compte de stockage Azure. Pour en savoir plus, voir la section [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour un nouveau disque, vous n’avez pas besoin de le créer au préalable, car Azure le crée lorsque vous l’attachez.
* Pour un disque existant, le fichier .vhd doit être disponible dans un compte de stockage Azure. Vous pouvez utiliser un fichier .vhd présent, s’il n’est attaché à aucune autre machine virtuelle, ou charger votre propre fichier .vhd sur le compte de stockage.


## <a name="find-the-virtual-machine"></a>Recherchez la machine virtuelle.
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu Hub, cliquez sur **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Dans le panneau Machines virtuelles, dans **Essentials**, cliquez sur **Disques**.
   
    ![Ouverture des paramètres d’un disque](./media/virtual-machines-linux-attach-disk-portal/find-disk-settings.png)

Continuez en suivant les instructions pour attacher un [nouveau disque](#option-1-attach-a-new-disk) ou un [disque existant](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-a-new-disk"></a>Option 1 : attacher un nouveau disque
1. Dans le panneau **Disques**, cliquez sur **Attacher un nouveau disque**.
2. Passez en revue les paramètres par défaut, mettez-les à jour en fonction des besoins, puis cliquez sur **OK**.
   
   ![Examen des paramètres d’un disque](./media/virtual-machines-linux-attach-disk-portal/attach-new.png)
3. Après qu’Azure a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

## <a name="option-2-attach-an-existing-disk"></a>Option 2 : attacher un disque existant
1. Dans le panneau **Disques**, cliquez sur **Attacher un disque existant**.
2. Sous **Attacher un disque existant**, cliquez sur **Fichier VHD**.
   
   ![Attachement d’un disque existant](./media/virtual-machines-linux-attach-disk-portal/attach-existing.png)
3. Sous **Comptes de stockage**, sélectionnez le compte et le conteneur dans lequel figure le fichier .vhd.
   
   ![Recherche d’emplacement VHD](./media/virtual-machines-linux-attach-disk-portal/find-storage-container.png)
4. Sélectionnez le fichier .vhd.
5. Sous **Attacher un disque existant**, le fichier que vous venez de sélectionner est répertorié sous **Fichier VHD**. Cliquez sur **OK**.
6. Après qu’Azure a attaché le disque à la machine virtuelle, le disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.



## <a name="next-steps"></a>Étapes suivantes
Une fois le disque ajouté, vous devez le préparer pour utilisation. Pour plus d'informations, consultez [Initialisation d’un nouveau disque de données sous Linux](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux).



<!--HONumber=Nov16_HO5-->


