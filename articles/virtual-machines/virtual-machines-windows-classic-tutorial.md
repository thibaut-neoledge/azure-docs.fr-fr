---
title: "Créer une machine virtuelle dans le portail Classic | Microsoft Docs"
description: "Création d&quot;une machine virtuelle Windows dans le portail Azure Classic."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: ede78ff35ec6e7007e7a1c7b946c8e17bd425f37


---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Créer une machine virtuelle exécutant Windows dans le portail Azure Classic
> [!div class="op_single_selector"]
> * [Portail Azure Classic](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [PowerShell : Déploiement classique](virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br>

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [effectuer ces étapes à l’aide du modèle de déploiement Resource Manager](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à l’aide du **nouveau portail Azure**. 

Ce didacticiel vous montre comment créer une machine virtuelle Azure exécutant Windows dans le portail Azure Classic. Nous allons utiliser une image Windows Server comme exemple, mais il s’agit simplement d’un des nombreux types d’images proposés par Azure. Notez que votre choix en matière d’images dépend de votre abonnement. Par exemple, les images de bureau Windows peuvent être accessibles aux abonnés MSDN.

Cette section vous montre comment utiliser l’option **À partir de la galerie** dans le portail classique Azure pour créer la machine virtuelle. Cette option propose davantage de possibilités de configuration que l'option **Création rapide** . Par exemple, si vous voulez associer une machine virtuelle à un réseau virtuel, vous devez utiliser l'option **À partir de la galerie** .

Vous pouvez également créer des machines virtuelles en utilisant [vos propres images](virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Pour en savoir plus à ce sujet et connaître d’autres méthodes, consultez [les différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="video-walkthrough"></a>Vidéo de procédure pas à pas
Voici une procédure pas à pas de ce didacticiel.

[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Creating-a-Windows-VM-on-Microsoft-Azure-GA-Portal/player]


## <a id="createvirtualmachine"> </a>Créer la machine virtuelle
[!INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [créer une machine virtuelle à l’aide du modèle de déploiement Resource Manager](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à l’aide du nouveau portail Azure. 
* Connectez-vous à votre machine virtuelle. Pour obtenir des instructions, consultez [Connexion à une machine virtuelle exécutant Windows Server](virtual-machines-windows-classic-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Attacher un disque pour stocker des données. Vous pouvez attacher des disques, qu'ils soient vides ou non. Pour obtenir des instructions, consultez la page [Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement Classic](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Dec16_HO1-->


