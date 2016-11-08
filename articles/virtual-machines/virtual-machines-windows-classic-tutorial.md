---
title: Créer une machine virtuelle exécutant Windows dans le portail classique | Microsoft Docs
description: Création d'une machine virtuelle Windows dans le portail Azure Classic.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Créer une machine virtuelle exécutant Windows dans le portail Azure Classic
> [!div class="op_single_selector"]
> * [Portail Azure Classic](virtual-machines-windows-classic-tutorial.md)
> * [PowerShell : Déploiement classique](virtual-machines-windows-classic-create-powershell.md)
> 
> 

<br>

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Découvrez comment [effectuer ces étapes à l’aide du modèle de déploiement de gestionnaire de ressources](virtual-machines-windows-hero-tutorial.md). Pour utiliser le nouveau portail Azure, consultez [Créer votre première machine virtuelle Windows dans le portail Azure](virtual-machines-windows-hero-tutorial.md).

Ce didacticiel vous montre à quel point il est facile de créer une machine virtuelle Azure exécutant Windows dans le portail Azure Classic. Nous allons utiliser une image Windows Server comme exemple, mais il s’agit simplement d’un des nombreux types d’images proposés par Azure. Notez que votre choix en matière d’images dépend de votre abonnement. Par exemple, les images de bureau Windows peuvent être accessibles aux abonnés MSDN.

Cette section vous montre comment utiliser l’option **À partir de la galerie** dans le portail classique Azure pour créer la machine virtuelle. Cette option propose davantage de possibilités de configuration que l'option **Création rapide** . Par exemple, si vous voulez associer une machine virtuelle à un réseau virtuel, vous devez utiliser l'option **À partir de la galerie** .

Vous pouvez également créer des machines virtuelles en utilisant [vos propres images](virtual-machines-windows-classic-createupload-vhd.md). Pour en savoir plus à ce sujet et connaître d’autres méthodes, consultez [les différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-creation-choices.md).

## <a name="video-walkthrough"></a>Vidéo de procédure pas à pas
Voici une procédure pas à pas de ce didacticiel.

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Creating-a-Windows-VM-on-Microsoft-Azure-GA-Portal/player]


## <a name="<a-id="createvirtualmachine">-</a>create-the-virtual-machine"></a><a id="createvirtualmachine"> </a>Créer la machine virtuelle
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Étapes suivantes
* Connectez-vous à votre machine virtuelle. Pour obtenir des instructions, consultez [Connexion à une machine virtuelle exécutant Windows Server](virtual-machines-windows-classic-connect-logon.md).
* Attacher un disque pour stocker des données. Vous pouvez attacher des disques, qu'ils soient vides ou non. Pour obtenir des instructions, consultez la page [Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement Classic](virtual-machines-windows-classic-attach-disk.md).

<!--HONumber=Oct16_HO2-->


