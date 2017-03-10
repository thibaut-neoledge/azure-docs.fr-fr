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
ms.date: 03/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: cc14f7747c4a24acea434f62b7615d10819bd619
ms.openlocfilehash: 31d7f4620420839ade1ca58391fad78e94d4e929
ms.lasthandoff: 02/15/2017


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Attachement d’un disque de données à une machine virtuelle Linux dans le portail Azure
Cet article vous explique comment attacher des disques nouveaux et existants à une machine virtuelle Linux par le biais du portail Azure. Vous pouvez également [attacher un disque de données à une machine virtuelle Windows dans le Portail Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vous pouvez utiliser des disques gérés Azure (Azure Managed Disks) ou des disques non gérés. Les disques gérés sont traités par la plateforme Azure et ne nécessitent pas de préparation ou d’emplacement pour les stocker. Les disques non gérés requièrent un compte de stockage et sont soumis à un certain nombre de [quotas et de limites](../azure-subscription-service-limits.md#storage-limits). Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble d’Azure Managed Disks](../storage/storage-managed-disks-overview.md).

Avant d’attacher des disques à votre machine virtuelle, lisez les conseils suivants :

* La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour utiliser le stockage Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Les disques attachés aux machines virtuelles sont en réalité des fichiers .vhd stockés dans Azure. Pour en savoir plus, voir la section [À propos des disques et VHD pour machines virtuelles](../storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Recherchez la machine virtuelle.
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu Hub, cliquez sur **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Dans le panneau Machines virtuelles, dans **Essentials**, cliquez sur **Disques**.
   
    ![Ouverture des paramètres d’un disque](./media/virtual-machines-linux-attach-disk-portal/find-disk-settings.png)

Continuez en suivant les instructions pour attacher un [disque géré](#use-azure-managed-disks) ou un [disque non géré](#use-unmanaged-disks).

## <a name="use-azure-managed-disks"></a>Utiliser Azure Managed Disks

### <a name="attach-a-new-disk"></a>Attacher un nouveau disque

1. Dans le panneau **Disques**, cliquez sur **+ Add data disk** (+ Ajouter un disque de données).
2. Cliquez sur le menu déroulant **Nom** et sélectionnez **Create disk** (Créer un disque) :

    ![Créer un disque géré Azure](./media/virtual-machines-linux-attach-disk-portal/create-new-md.png)

3. Entrez un nom pour votre disque géré. Vérifiez les paramètres par défaut, mettez-les à jour le cas échéant, puis cliquez sur **Créer**.
   
   ![Examen des paramètres d’un disque](./media/virtual-machines-linux-attach-disk-portal/create-new-md-settings.png)

4. Cliquez sur **Enregistrer** pour créer le disque géré et mettre à jour la configuration de la machine virtuelle :

   ![Enregistrer le nouveau disque géré Azure](./media/virtual-machines-linux-attach-disk-portal/confirm-create-new-md.png)

5. Après qu’Azure a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**. Étant donné que les disques gérés sont des ressources de niveau supérieur, le disque s’affiche à la racine du groupe de ressources :

   ![Disque géré Azure dans le groupe de ressources](./media/virtual-machines-linux-attach-disk-portal/view-md-resource-group.png)

### <a name="attach-an-existing-disk"></a>Association d'un disque existant
1. Dans le panneau **Disques**, cliquez sur **+ Add data disk** (+ Ajouter un disque de données).
2. Cliquez sur le menu déroulant **Nom** pour afficher une liste des disques gérés disponibles dans votre abonnement Azure. Sélectionnez le disque géré à attacher :

   ![Attacher un disque géré Azure existant](./media/virtual-machines-linux-attach-disk-portal/select-existing-md.png)

3. Cliquez sur **Enregistrer** pour attacher le disque géré existant et mettre à jour la configuration de la machine virtuelle :
   
   ![Enregistrer les mises à jour Azure Managed Disk](./media/virtual-machines-linux-attach-disk-portal/confirm-attach-existing-md.png)

4. Après qu’Azure a attaché le disque à la machine virtuelle, le disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

## <a name="use-unmanaged-disks"></a>Utiliser des disques non gérés

### <a name="attach-a-new-disk"></a>Attacher un nouveau disque

1. Dans le panneau **Disques**, cliquez sur **+ Add data disk** (+ Ajouter un disque de données).
2. Passez en revue les paramètres par défaut, mettez-les à jour en fonction des besoins, puis cliquez sur **OK**.
   
   ![Examen des paramètres d’un disque](./media/virtual-machines-linux-attach-disk-portal/attach-new.png)
3. Après qu’Azure a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

### <a name="attach-an-existing-disk"></a>Association d'un disque existant
1. Dans le panneau **Disques**, cliquez sur **+ Add data disk** (+ Ajouter un disque de données).
2. Sous **Attacher un disque existant**, cliquez sur **Fichier VHD**.
   
   ![Attachement d’un disque existant](./media/virtual-machines-linux-attach-disk-portal/attach-existing.png)
3. Sous **Comptes de stockage**, sélectionnez le compte et le conteneur dans lequel figure le fichier .vhd.
   
   ![Recherche d’emplacement VHD](./media/virtual-machines-linux-attach-disk-portal/find-storage-container.png)
4. Sélectionnez le fichier .vhd.
5. Sous **Attacher un disque existant**, le fichier que vous venez de sélectionner est répertorié sous **Fichier VHD**. Cliquez sur **OK**.
6. Après qu’Azure a attaché le disque à la machine virtuelle, le disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.


## <a name="next-steps"></a>Étapes suivantes
Une fois le disque ajouté, vous devez le préparer pour utilisation. Pour plus d'informations, consultez [Initialisation d’un nouveau disque de données sous Linux](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux).

