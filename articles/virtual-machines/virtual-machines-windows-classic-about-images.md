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
ms.date: 07/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 28720bcedb4df66c2f7434f25ed251d4e116c415


---
# <a name="about-images-for-windows-virtual-machines"></a>À propos des images pour les machines virtuelles Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Pour plus d’informations sur la recherche et l’utilisation d’images dans le modèle Resource Manager, suivez [ce lien](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Utilisation des images
Vous pouvez utiliser le module Azure PowerShell pour gérer les images disponibles dans votre abonnement Azure. Vous pouvez également utiliser le portail Azure Classic pour certaines tâches d’image, mais la ligne de commande vous offre davantage d’options.

* **Obtenir toutes les images** : `Get-AzureVMImage` renvoie la liste de toutes les images disponibles dans votre abonnement actuel : vos images ainsi que celles fournies par Azure ou ses partenaires. En d’autres termes, il se peut que votre liste soit volumineuse. Les exemples suivants montrent comment obtenir une liste plus courte.
* **Obtenir des familles d’images** : `Get-AzureVMImage | select ImageFamily` obtient la liste des familles d’images en affichant des propriétés de chaînes **ImageFamily**.
* **Obtenir toutes les images dans une famille spécifique** : `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Trouver des images de machines virtuelles** : `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` cela fonctionne en filtrant la propriété DataDiskConfiguration, qui s’applique uniquement aux images de machines virtuelles. Cet exemple filtre également les résultats uniquement en fonction du libellé et du nom de l’image.
* **Enregistrer une image généralisée** : `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Enregistrer une image spécialisée** : `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
  >[Azure.Tip] Le paramètre OSState est requis si vous souhaitez créer une image de machine virtuelle, qui inclut des disques de données ainsi que le disque du système d'exploitation. Si vous n'utilisez pas le paramètre, l'applet de commande crée une image de système d'exploitation. La valeur du paramètre indique si l'image est généralisée ou spécialisée, selon que le disque du système d'exploitation a été préparé pour une réutilisation.
* **Supprimer une image** : `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également [créer une machine Windows à l’aide du portail Classic](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)




<!--HONumber=Nov16_HO3-->


