<properties
	pageTitle="À propos des images pour les machines virtuelles Windows | Microsoft Azure"
	description="En savoir plus sur l'utilisation des images avec des machines virtuelles Windows dans Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# À propos des images pour les machines virtuelles Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## Utilisation des images

Vous pouvez utiliser le module Azure PowerShell pour gérer les images disponibles dans votre abonnement Azure. Vous pouvez également utiliser le portail Azure Classic pour certaines tâches d’image, mais la ligne de commande vous offre davantage d’options.


-	**Obtenir toutes les images** : `Get-AzureVMImage` renvoie une liste de toutes les images disponibles dans votre abonnement actuel, à savoir vos images ainsi que celles fournies par Azure ou des partenaires. En d’autres termes, il se peut que votre liste soit volumineuse. Les exemples suivants montrent comment obtenir une liste plus courte.
-	**Obtenir des familles d’image** : `Get-AzureVMImage | select ImageFamily` obtient une liste des familles de l’image en affichant des propriétés de chaînes **ImageFamily**.
-	**Obtenir toutes les images dans une famille spécifique** : `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**Trouver des images de machine virtuelle** : `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` cela fonctionne en filtrant la propriété DataDiskConfiguration, qui s’applique uniquement aux images de machine virtuelle. Cet exemple filtre également les résultats uniquement en fonction du libellé et du nom de l’image.
-	**Enregistrer une image généralisée** : `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**Enregistrer une image spécialisée** : `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] Le paramètre OSState est requis si vous souhaitez créer une image de machine virtuelle, qui inclut des disques de données ainsi que le disque du système d'exploitation. Si vous n'utilisez pas le paramètre, l'applet de commande crée une image de système d'exploitation. La valeur du paramètre indique si l'image est généralisée ou spécialisée, selon que le disque du système d'exploitation a été préparé pour une réutilisation.
-	**Suppression d’une image** : `Remove-AzureVMImage –ImageName "MyOldVmImage"`


## Étapes suivantes

Vous pouvez également [créer une machine Windows à l’aide du portail classique](virtual-machines-windows-classic-tutorial.md)

<!---HONumber=AcomDC_0727_2016-->