---
title: "Parcourir et sélectionner des images de machines virtuelles Windows | Microsoft Docs"
description: "Découvrez comment déterminer l’éditeur, l’offre et la référence SKU pour des images quand vous créez une machine virtuelle Windows avec le modèle de déploiement de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b36fb2b94dcee94eb299221bbb5c82080d23bfb1
ms.lasthandoff: 03/31/2017


---
# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell"></a>Parcourir et sélectionner des images de machines virtuelles Windows dans Azure avec PowerShell
Cette rubrique décrit comment rechercher des éditeurs d’images de machines virtuelles, des offres, des références et des versions pour chaque emplacement vers lequel vous souhaitez effectuer un déploiement. À titre d’exemple, voici certaines images de machines virtuelles Windows couramment utilisées :

## <a name="table-of-commonly-used-windows-images"></a>Tableau des images système Windows couramment utilisées
| PublisherName | Offer | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Entreprise, optimisé pour l’entrepôt de données |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Entreprise, optimisé pour le traitement transactionnel en ligne |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftWindowsServer |WindowsServer |Windows Server Technical Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

## <a name="find-azure-images-with-powershell"></a>Rechercher des images Azure avec PowerShell
> [!NOTE]
> Installez et configurez la [dernière version d’Azure PowerShell](/powershell/azureps-cmdlets-docs). Si vous utilisez des modules Azure PowerShell antérieurs à la version 1.0, vous vous servez des commandes suivantes, mais devez d’abord `Switch-AzureMode AzureResourceManager`. 
> 
> 

Lorsque vous créez une machine virtuelle avec le gestionnaire des ressources Azure, vous devez, dans certains cas, spécifier une image en combinant les propriétés d'image suivantes :

* Éditeur
* Offer
* SKU

Par exemple, ces valeurs sont nécessaires pour l’applet de commande PowerShell `Set-AzureRMVMSourceImage` ou avec un fichier de modèle de groupe de ressources dans lequel vous devez spécifier le type de machine virtuelle à créer.

Si vous devez déterminer ces valeurs, vous pouvez parcourir les images à cette fin en procédant ainsi :

1. en répertoriant les éditeurs d’images ;
2. pour un éditeur donné, en répertoriant ses offres ;
3. pour une offre donnée, en répertoriant ses références SKU.

Tout d’abord, répertoriez les serveurs de publication avec les commandes suivantes :

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Indiquez le nom d’éditeur de publication choisi et exécutez les commandes suivantes :

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Indiquez le nom de l’offre choisi et exécutez les commandes suivantes :

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

L’affichage de la commande `Get-AzureRMVMImageSku` comprend toutes les informations dont vous avez besoin pour spécifier l’image pour une nouvelle machine virtuelle.

Vous trouverez ci-dessous un exemple complet :

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Pour l'éditeur « MicrosoftWindowsServer » :

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

Pour l'offre « WindowsServer » :

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

Dans cette liste, copiez le nom de référence SKU choisi. Vous disposez de toutes les informations pour l’applet de commande PowerShell `Set-AzureRMVMSourceImage` ou pour un modèle de groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant choisir précisément l'image que vous voulez utiliser. Pour créer rapidement une machine virtuelle en utilisant les informations d’image que vous venez de trouver, ou pour utiliser un modèle avec ces informations d’image, consultez [Création d'une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
