---
title: "Créer une image RemoteApp Azure basée sur une machine virtuelle Azure | Microsoft Docs"
description: "Apprenez à créer une image Azure RemoteApp à partir d’une machine virtuelle Azure."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d41583ef-6cd8-4115-8dcb-b2cd5b3d301a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: c7ecd590503fcd2bc3ba06919a2f1a5c0fd0943e
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Création d’une image Azure RemoteApp basée sur une machine virtuelle Azure
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Vous pouvez créer des images Azure RemoteApp (qui contiennent les applications que vous partagez dans votre collection) à partir d'une machine virtuelle Azure. Vous pouvez également choisir d'utiliser une image de machine virtuelle que nous avons ajoutée à la galerie d’images de machines virtuelles Azure qui répond à toutes les exigences d’image Azure RemoteApp : vous pouvez utiliser cette image de machine virtuelle comme point de départ pour votre propre machine virtuelle, si vous le souhaitez. Recherchez simplement l’image « Hôte de session de Bureau à distance Windows Server » dans la bibliothèque.

La création de votre propre image basée sur une machine virtuelle Azure est un processus à deux étapes : la création de l’image et son téléchargement de la bibliothèque de machine virtuelle Azure vers Azure RemoteApp.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Créez une image personnalisée basée sur une machine virtuelle Azure
Utilisez ces étapes pour créer une image basée sur une machine virtuelle Azure.

1. Créez une machine virtuelle Azure. Vous pouvez utiliser l’image « Hôte de session de Bureau à distance Windows Server » ou « Hôte de session de Bureau à distance Windows Server avec Microsoft Office 365 ProPlus » à partir de la galerie d’images de la machine virtuelle Azure. Cette image répond à toutes les exigences d’image de modèle Azure RemoteApp.
   
    Pour plus d’informations, consultez la section [Création d’une machine virtuelle exécutant Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Connectez-vous à la machine virtuelle, puis installez et configurez les applications que vous souhaitez partager via RemoteApp. Exécutez les éventuelles opérations de configuration Windows supplémentaires requises par vos applications.
   
    Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Windows Server](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Si vous utilisez l’une des images de l’hôte de session Bureau à distance Windows Server, elle comprend un script de validation afin de garantir que votre machine virtuelle répond aux exigences de RemoteApp. Pour exécuter le script, double-cliquez sur **ValidateRemoteAppImage** sur le bureau. Assurez-vous que toutes les erreurs signalées par le script sont résolues avant de passer à l’étape suivante.
4. SYSPREP généralise et capture l’image. Pour obtenir des instructions, consultez la section [Capture d’une machine virtuelle Windows pour l’utiliser comme modèle](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importation de l’image dans la bibliothèque d’images Azure RemoteApp
Utilisez ces étapes pour importer la nouvelle image dans Azure RemoteApp :

1. Dans l’onglet **Images de modèle** :
   
   * Si vous ne disposez pas d’images existantes, cliquez sur **Télécharger ou importer une image de modèle**.
   * Si vous disposez déjà d’au moins une image, cliquez sur **+** pour ajouter une nouvelle image.
2. Sélectionnez **Importer une image à partir de votre bibliothèque de machines virtuelles**, puis cliquez sur **Suivant**.
3. Sur la page suivante, sélectionnez votre image personnalisée dans la liste et confirmez que vous avez suivi les étapes répertoriées lors de la création de votre image. Cliquez sur **Suivant**.
4. Entrez un nom pour la nouvelle image RemoteApp et choisissez l’emplacement, puis cliquez sur la coche pour démarrer le processus d’importation.

> [!NOTE]
> Vous pouvez importer des images à partir de tout emplacement Azure pris en charge par les machines virtuelles Azure vers tout emplacement Azure pris en charge par Azure RemoteApp. Selon les emplacements, l’importation peut durer jusqu’à 25 minutes.
> 
> 

Vous êtes maintenant prêt à créer votre collection, [cloud](remoteapp-create-cloud-deployment.md) ou [hybride](remoteapp-create-hybrid-deployment.md), selon vos besoins.


