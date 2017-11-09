---
title: "Attacher un disque de données à une machine virtuelle Linux | Microsoft Docs"
description: "Utilisez le portail pour attacher un disque de données nouveau ou existant à une machine virtuelle Linux."
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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 1a7c98207a5f19d514a0cd05b66898e7fb725944
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2017
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utiliser le portail pour attacher un disque de données à une machine virtuelle Linux 
Cet article vous explique comment attacher des disques nouveaux et existants à une machine virtuelle Linux par le biais du portail Azure. Vous pouvez également [attacher un disque de données à une machine virtuelle Windows dans le Portail Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Avant d’attacher des disques à votre machine virtuelle, lisez les conseils suivants :

* La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pour utiliser le stockage Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Les disques attachés aux machines virtuelles sont en réalité des fichiers .vhd stockés dans Azure. Pour en savoir plus, voir la section [À propos des disques et VHD pour machines virtuelles](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Recherchez la machine virtuelle.
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu de gauche, cliquez sur **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Dans la page Machines virtuelles, dans **Essentials**, cliquez sur **Disques**.
   
    ![Ouverture des paramètres d’un disque](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Attacher un nouveau disque

1. Dans le panneau **Disques**, cliquez sur **+ Ajouter un disque de données**.
2. Cliquez sur le menu déroulant **Nom** et sélectionnez **Create disk** (Créer un disque) :

    ![Créer un disque géré Azure](./media/attach-disk-portal/create-new-md.png)

3. Entrez un nom pour votre disque géré. Vérifiez les paramètres par défaut, mettez-les à jour le cas échéant, puis cliquez sur **Créer**.
   
   ![Examen des paramètres d’un disque](./media/attach-disk-portal/create-new-md-settings.png)

4. Cliquez sur **Enregistrer** pour créer le disque géré et mettre à jour la configuration de la machine virtuelle :

   ![Enregistrer le nouveau disque géré Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Après qu’Azure a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**. Étant donné que les disques gérés sont des ressources de niveau supérieur, le disque s’affiche à la racine du groupe de ressources :

   ![Disque géré Azure dans le groupe de ressources](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Association d'un disque existant
1. Dans le panneau **Disques**, cliquez sur **+ Ajouter un disque de données**.
2. Cliquez sur le menu déroulant **Nom** pour afficher une liste des disques gérés disponibles dans votre abonnement Azure. Sélectionnez le disque géré à attacher :

   ![Attacher un disque géré Azure existant](./media/attach-disk-portal/select-existing-md.png)

3. Cliquez sur **Enregistrer** pour attacher le disque géré existant et mettre à jour la configuration de la machine virtuelle :
   
   ![Enregistrer les mises à jour Azure Managed Disk](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Après qu’Azure a attaché le disque à la machine virtuelle, le disque est répertorié dans les paramètres de disque de la machine virtuelle sous **Disques de données**.



## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également [attacher un disque de données](add-disk.md) à l’aide d’Azure CLI.
