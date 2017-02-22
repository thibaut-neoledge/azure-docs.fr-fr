---
title: "Capture d’une image de machine virtuelle Microsoft Azure | Microsoft Docs"
description: "Capturer l’image d’une machine virtuelle Microsoft Azure créée avec le modèle de déploiement classique"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: 6b68d41daeea780d70b5ce1389d05f1f4fdf65ea


---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capturer l’image d’une machine virtuelle Microsoft Azure créée avec le modèle de déploiement classique
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur le modèle Resource Manager, consultez [Créer une copie d’une machine virtuelle Windows dans Azure](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Cet article vous montre comment capturer une machine virtuelle Azure exécutant Windows de façon à l’utiliser comme image pour créer d’autres machines virtuelles. Cette image contient le disque du système d’exploitation et les disques de données éventuellement associés à la machine virtuelle. Comme elle ne comporte pas de configurations de mise en réseau, vous devez effectuer ces tâches de configuration quand vous créez d’autres machines virtuelles à partir de l’image.

Azure stocke l’image sous **Mes images**. Il s’agit de l’emplacement où sont stockées les images que vous avez éventuellement téléchargées. Pour plus d’informations sur les images, consultez la page [À propos des images pour les machines virtuelles](virtual-machines-linux-classic-about-images.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Avant de commencer
Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d’exploitation et attaché les disques de données. Si vous ne l’avez pas encore fait, voir ces instructions :

* [Création d’une machine virtuelle à partir d’une image](virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Comment attacher un disque de données à une machine virtuelle](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* Assurez-vous que les rôles serveur sont pris en charge avec Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Ce processus supprime la machine virtuelle d’origine une fois celle-ci capturée. 
> 
> 

Avant de capturer une image d’une machine virtuelle Azure, nous vous recommandons de sauvegarder la machine virtuelle cible. Les machines virtuelles Azure peuvent être sauvegardées à l’aide d’Azure Backup. Pour plus d’informations, voir [Sauvegarde des machines virtuelles Azure](../backup/backup-azure-vms.md). D’autres solutions sont disponibles auprès de partenaires certifiés. Pour savoir ce qui est actuellement disponible, faites une recherche dans Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Capture de la machine virtuelle
1. Dans le [portail Azure Classic](http://manage.windowsazure.com), **connectez-vous** à la machine virtuelle. Pour obtenir des instructions, consultez la rubrique [Comment se connecter à une machine virtuelle exécutant Windows Server][How to sign in to a virtual machine running Windows Server].
2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
3. Accédez au répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.
4. La boîte de dialogue **Outil de préparation système** apparaît. Effectuez les actions suivantes :
   
   * Dans **Action de nettoyage du système**, sélectionnez **Entrer en mode OOBE (OOBE)** et vérifiez que la case à cocher **Généraliser** est activée. Pour plus d’informations sur l’utilisation de Sysprep, consultez la page [Introduction à l’utilisation de Sysprep][How to Use Sysprep: An Introduction].
   * Dans **Options d’arrêt**, sélectionnez **Arrêter**.
   * Cliquez sur **OK**.
   
   ![Exécutez Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)
5. Sysprep arrête la machine virtuelle, ce qui a pour effet de modifier l’état de celle-ci en **Arrêté**dans le portail Azure Classic.
6. Dans le portail Azure Classic, cliquez sur **Machines virtuelles** , puis sélectionnez la machine virtuelle à capturer.
7. Dans la barre de commandes, cliquez sur **Capture**.
   
   ![Capture machine virtuelle](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)
   
   La boîte de dialogue **Capture the Virtual Machine** s’affiche.
8. Dans **Image Name**, entrez le nom de la nouvelle image.
9. Avant d’ajouter une image Windows Server à votre jeu d’images personnalisées, elle doit être généralisée en exécutant Sysprep comme indiqué dans les étapes précédentes. Cliquez sur **J’ai exécuté Sysprep sur la machine virtuelle** pour indiquer que vous l’avez fait.
10. Cliquez sur la coche pour capturer l’image. La nouvelle image est disponible dans **Images**.
    
    ![Capture d’image réussie](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Étapes suivantes
L’image est prête à être utilisée pour créer des machines virtuelles. Pour cela, créez une machine virtuelle en utilisant l’élément de menu **À partir de la galerie** , puis en sélectionnant l’image que vous venez de créer. Pour obtenir des instructions, consultez [Création d’une machine virtuelle à partir d’une image](virtual-machines-windows-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[How to sign in to a virtual machine running Windows Server]: virtual-machines-windows-classic-connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png



<!--HONumber=Dec16_HO1-->


