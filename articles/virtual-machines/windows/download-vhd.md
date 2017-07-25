---
title: "Télécharger un VHD Windows à partir d’Azure | Microsoft Docs"
description: "Téléchargez un VHD Windows à l’aide du Portail Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: a7370598dd6570d2e124ed2a10d92cfcd1d5087a
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017

---

# <a name="download-a-windows-vhd-from-azure"></a>Télécharger un VHD Windows à partir d’Azure

Dans cet article, vous apprendrez à télécharger un fichier de [disque dur virtuel (VHD) Windows](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à partir d’Azure à l’aide du Portail Azure. 

Les machines virtuelles dans Azure utilisent des [disques](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) comme emplacement de stockage d’un système d’exploitation, d’applications et de données. Toutes les machines virtuelles Azure possèdent au moins deux disques : un disque de système d’exploitation Windows et un disque temporaire. Le disque de système d’exploitation est initialement créé à partir d’une image. Tant le disque que l’image sont des VHD stockés dans un compte de stockage Azure. Les machines virtuelles peuvent également disposer d’un ou plusieurs disques de données, également stockés sur les VHD.

## <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.

Il n’est pas possible de télécharger un VHD attaché à une machine virtuelle en cours d’exécution à partir d’Azure. Il vous faut arrêter la machine virtuelle pour télécharger un VHD. Si vous souhaitez utiliser un VHD en tant [qu’image](tutorial-custom-images.md) afin de créer d’autres machines virtuelles avec de nouveaux disques, utilisez [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) pour généraliser le système d’exploitation contenu dans le fichier, puis arrêtez la machine virtuelle. Pour utiliser le VHD en tant que disque d’une nouvelle instance d’une machine virtuelle ou d’un disque de données existant, il vous suffit d’arrêter et de libérer de la machine virtuelle.

Pour utiliser le VHD en tant qu’image pour créer d’autres machines virtuelles, suivez les étapes ci-dessous :

1.  Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/).
2.  [Connectez-vous à la machine virtuelle](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Sur la machine virtuelle, ouvrez la fenêtre d’invite de commandes en tant qu’administrateur.
4.  Remplacez le répertoire par *%windir%\system32\sysprep* et exécutez sysprep.exe.
5.  Dans la boîte de dialogue Outil de préparation du système, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience) du système** et vérifiez que **Généraliser** est sélectionné.
6.  Dans Options d’arrêt, sélectionnez **Arrêter**, puis cliquez sur **OK**. 

Pour utiliser le VHD en tant que disque d’une nouvelle instance d’une machine virtuelle ou d’un disque de données existant, suivez les étapes ci-dessous :

1.  Dans le menu Hub du Portail Azure, cliquez sur **Machines virtuelles**.
2.  Sélectionnez la machine virtuelle dans la liste.
3.  Dans le panneau de la machine virtuelle, cliquez sur **Arrêter**.

    ![Arrêter la machine virtuelle](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Générer une URL de SAP

Pour télécharger le fichier de VHD, vous devez générer une URL de [signature d’accès partagé (SAP)](../../storage/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Un délai d’expiration est affecté à l’URL lors de sa génération.

1.  Dans le menu du panneau de la machine virtuelle, cliquez sur **Disques**.
2.  Sélectionnez le disque de système d’exploitation de la machine virtuelle, puis cliquez sur **Exporter**.
3.  Fixez le délai d’expiration de l’URL sur *36000*.
4.  Cliquez sur **Générer l’URL**.

    ![Générer une URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> Le délai d’expiration est augmenté par rapport à la valeur par défaut afin de laisser suffisamment de temps pour télécharger le fichier volumineux de VHD pour un système d’exploitation Windows Server. En général, le téléchargement d’un fichier de VHD contenant le système d’exploitation Windows prend plusieurs heures, en fonction de la connexion. Si vous téléchargez un VHD pour un disque de données, le délai par défaut est suffisant. 
> 
> 

## <a name="download-vhd"></a>Télécharger un VHD

1.  Sous l’URL générée, cliquez sur Télécharger le fichier de VHD.

    ![Télécharger un VHD](./media/download-vhd/export-download.png)

2.  Vous devrez peut-être cliquer sur **Enregistrer** dans le navigateur pour commencer le téléchargement. Le nom par défaut du fichier de VHD est *abcd*.

    ![Cliquez sur Enregistrer dans le navigateur](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [charger un fichier de VHD sur Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Créez des disques gérés à partir de disques non gérés dans un compte de stockage](create-managed-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Gérez des disques Azure avec PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


