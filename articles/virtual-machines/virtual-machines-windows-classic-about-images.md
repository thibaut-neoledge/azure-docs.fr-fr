---
title: "À propos des images pour les machines virtuelles Windows | Microsoft Docs"
description: En savoir plus sur l&quot;utilisation des images avec des machines virtuelles Windows dans Azure.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 91080c3d6f05bbafa80b5b14d2ce840b3432110d
ms.lasthandoff: 03/22/2017


---
# <a name="about-images-for-windows-virtual-machines"></a>À propos des images pour les machines virtuelles Windows
> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur la recherche et l’utilisation d’images dans le modèle Resource Manager, suivez [ce lien](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Utilisation des images

Vous pouvez utiliser le module Azure PowerShell et le portail Azure pour gérer les images disponibles dans votre abonnement Azure. Le module Azure PowerShell fournit plus d’options de commande, ce qui vous permet d’identifier exactement ce que vous voulez voir ou faire. Le portail Azure offre une interface graphique utilisateur pour nombre de tâches administratives quotidiennes.

Voici quelques exemples qui utilisent le module Azure PowerShell.

* **Obtenir toutes les images** : `Get-AzureVMImage` retourne la liste de toutes les images disponibles dans votre abonnement actuel : vos images et celles fournies par Azure ou ses partenaires. La liste qui s’affiche peut être volumineuse. Les exemples suivants montrent comment obtenir une liste plus courte.
* **Obtenir des familles d’images** : `Get-AzureVMImage | select ImageFamily` obtient la liste des familles d’images en affichant des propriétés de chaînes **ImageFamily**.
* **Obtenir toutes les images dans une famille spécifique** : `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Trouver des images de machines virtuelles** : `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` cette applet de commande fonctionne en filtrant la propriété DataDiskConfiguration, qui s’applique uniquement aux images de machines virtuelles. Cet exemple filtre également les résultats uniquement en fonction du libellé et du nom de l’image.
* **Enregistrer une image généralisée** : `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Enregistrer une image spécialisée** : `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > Le paramètre OSState est requis pour créer une image de machine virtuelle, qui inclut le disque de système d’exploitation et les disques de données associés. Si vous n'utilisez pas le paramètre, l'applet de commande crée une image de système d'exploitation. La valeur du paramètre indique si l'image est généralisée ou spécialisée, selon que le disque du système d'exploitation a été préparé pour une réutilisation.

* **Supprimer une image** : `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également [créer une machine Windows à l’aide du portail Azure](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

