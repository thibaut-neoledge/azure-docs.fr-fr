---
title: "Généraliser une machine virtuelle Windows à utiliser dans Azure | Microsoft Docs"
description: "Apprenez à utiliser Sysprep pour généraliser une machine virtuelle Windows afin de l’utiliser avec le modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a745d400-c8be-48b4-a891-4a18495ef3fd
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: a0fca1e2a1d354be9ad34fb3a36714d0bdf393d2
ms.lasthandoff: 03/06/2017


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Généraliser une machine virtuelle Windows avec Sysprep
Cette section vous montre comment généraliser votre machine virtuelle Windows de façon à l’utiliser comme image. Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Si vous exécutez Sysprep avant de charger votre disque dur virtuel vers Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avant d’exécuter Sysprep. 
> 
> 

1. Connectez-vous à la machine virtuelle Windows
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
5. Cliquez sur **OK**.
   
    ![Démarrer Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)
6. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. 

> [!IMPORTANT]
> Ne redémarrez pas la machine virtuelle tant que vous n’avez pas terminé de télécharger le disque dur virtuel dans Azure ou de créer une image à partir de la machine virtuelle. Si la machine virtuelle est accidentellement redémarrée, exécutez Sysprep pour la généraliser à nouveau.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* S’il s’agit d’une machine virtuelle locale, vous pouvez à présent [charger le disque dur virtuel sur Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Si la machine virtuelle est déjà dans Azure, vous pouvez à présent [créer une image à partir de la machine virtuelle généralisée](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


