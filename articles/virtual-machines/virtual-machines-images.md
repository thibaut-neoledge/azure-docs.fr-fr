<properties
	pageTitle="À propos des images pour les machines virtuelles"
	description="En savoir plus sur l'utilisation des images avec des machines virtuelles dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/13/2015"
	ms.author="cynthn"/>

# À propos des images pour les machines virtuelles

Les images sont utilisées dans Azure pour fournir une nouvelle machine virtuelle avec un système d’exploitation. Une image peut également disposer d’un ou de plusieurs disques de données. Les images sont disponibles à partir de plusieurs sources :

-	Des images Azure sont disponibles dans le [Marketplace](http://azure.microsoft.com/gallery/virtual-machines/). Vous trouverez les dernières versions de Windows Server et des distributions du système d'exploitation Linux. Certaines images contiennent également des applications, telles que SQL Server. Les abonnés à MSDN Benefit et au paiement à l’utilisation MSDN ont accès à des images supplémentaires.
-	La communauté open source propose des images via le [dépôt de machines virtuelles](http://vmdepot.msopentech.com/List/Index).
-	Vous pouvez également stocker et utiliser vos propres images dans Azure, en capturant une machine virtuelle Azure existante pour une utilisation en tant qu'image ou en téléchargeant une image.

## À propos des images de machine virtuelle et des images de système d'exploitation

Deux types d'images peuvent être utilisés dans Azure : l’*image de machine virtuelle* et l’*image du système d'exploitation*. Une image de machine virtuelle inclut un système d'exploitation et tous les disques attachés à une machine virtuelle lors de la création de l'image. Il s’agit du type d’image le plus récent. Avant l'introduction d'images de machine virtuelle, une image dans Azure ne pouvait avoir qu’un système d'exploitation généralisé et aucun disque supplémentaire. Une image de machine virtuelle qui contient uniquement un système d’exploitation généralisé est en fait identique au type d’image d’origine, c’est-à-dire l’image du système d’exploitation.

Vous pouvez créer vos propres images, basées sur une machine virtuelle dans Azure ou une machine virtuelle exécutée dans un autre emplacement et que vous avez copiée et téléchargée. Si vous souhaitez utiliser une image pour créer plus d'une machine virtuelle, vous devez préparer son utilisation en tant qu'image en la généralisant. Pour créer une image Windows Server, exécutez la commande Sysprep sur le serveur pour le généraliser avant de télécharger le fichier .vhd. Pour plus d’informations sur Sysprep, consultez [Introduction à l’utilisation de Sysprep](http://go.microsoft.com/fwlink/p/?LinkId=392030). Pour créer une image de Linux, en fonction de la distribution de logiciels, vous devrez exécuter un ensemble de commandes spécifiques à la distribution et exécuter l’agent Azure Linux.

## Utilisation des images

Vous pouvez utiliser l'Interface de ligne de commande Azure (CLI) pour le module Mac, Linux et Windows ou Azure PowerShell pour gérer les images disponibles dans votre abonnement Azure. Vous pouvez également utiliser le portail Azure pour certaines tâches d'image, mais la ligne de commande vous offre davantage d'options.

Pour plus d'informations sur l'utilisation de ces outils avec les déploiements de Resource Manager, consultez [Navigation et sélection des images de machines virtuelles Azure avec PowerShell et la ligne de commande Azure](resource-groups-vm-searching.md).

Pour obtenir des exemples sur l'utilisation des outils dans un déploiement classique :

- Pour la ligne de commande, consultez « Commandes pour gérer vos images de machine virtuelle Azure » dans [Utilisation de la ligne de commande Azure pour Mac, Linux et Windows et gestion des services Azure](virtual-machines-command-line-tools.md)
- Pour Azure PowerShell, consultez la liste de commandes suivantes. Pour obtenir un exemple de recherche d'image pour créer une machine virtuelle, consultez « Étape 3 : déterminer le paramètre ImageFamily » dans [Utilisation d’Azure PowerShell pour créer et de préconfigurer des machines virtuelles basées sur Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

-	**Obtenir toutes les images** : `Get-AzureVMImage` renvoie une liste de toutes les images disponibles dans votre abonnement actuel, à savoir vos images ainsi que celles fournies par Azure ou des partenaires. En d’autres termes, il se peut que votre liste soit volumineuse. Les exemples suivants montrent comment obtenir une liste plus courte.
-	**Obtenir des familles d'image** :`Get-AzureVMImage | select ImageFamily` obtient une liste des familles de l'image en affichant des propriétés de chaînes **ImageFamily**.
-	**Obtenir toutes les images dans une famille spécifique** : `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-	**Trouver des images de machine virtuelle** : `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` cela fonctionne en filtrant la propriété DataDiskConfiguration, qui s'applique uniquement aux images de machine virtuelle. Cet exemple filtre également les résultats uniquement en fonction du libellé et du nom de l’image.
-	**Enregistrer une image généralisée** : `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-	**Enregistrer une image spécialisée** : `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip]Le paramètre OSState est requis si vous souhaitez créer une image de machine virtuelle, qui inclut des disques de données ainsi que le disque du système d'exploitation. Si vous n'utilisez pas le paramètre, l'applet de commande crée une image de système d'exploitation. La valeur du paramètre indique si l'image est généralisée ou spécialisée, selon que le disque du système d'exploitation a été préparé pour une réutilisation.
-	**Suppression d’une image** : `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## Ressources supplémentaires

[Différentes façons de créer une machine virtuelle Linux](virtual-machines-linux-choices-create-vm.md)

[Les différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=Sept15_HO3-->