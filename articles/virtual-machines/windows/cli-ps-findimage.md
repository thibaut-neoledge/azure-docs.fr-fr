---
title: "Sélectionner des images de machine virtuelle Windows dans Azure | Microsoft Docs"
description: "Découvrez comment utiliser Azure PowerShell pour déterminer l’éditeur, l’offre, la référence (SKU) et la version d’images de machine virtuelle de la Place de marché."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 630f555b003b0efc45b372a7009dbf036aa8c737
ms.contentlocale: fr-fr
ms.lasthandoff: 07/14/2017

---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Comment rechercher des images de machine virtuelle Windows sur la Place de marché Microsoft Azure avec Azure PowerShell

Cette rubrique décrit comment utiliser Azure PowerShell pour rechercher des images de machine virtuelle sur la Place de marché Microsoft Azure. Ces informations permettent de spécifier une image de Place de marché lorsque vous créez une machine virtuelle Windows.

Vérifiez que le dernier [module Azure PowerShell](/powershell/azure/install-azurerm-ps) est installé et configuré.



## <a name="table-of-commonly-used-windows-images"></a>Tableau des images système Windows couramment utilisées
| PublisherName | Offer | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-centre-de-données |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-centre-de-données-avec-conteneurs |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Entreprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Entreprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>Recherche d’images spécifiques


Lorsque vous créez une machine virtuelle avec le gestionnaire des ressources Azure, vous devez, dans certains cas, spécifier une image en combinant les propriétés d'image suivantes :

* Éditeur
* Offer
* SKU

Par exemple, ces valeurs sont nécessaires pour l’applet de commande PowerShell [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) ou avec un modèle de groupe de ressources dans lequel vous devez spécifier le type de machine virtuelle à créer.

Si vous avez besoin de déterminer ces valeurs, vous pouvez exécuter les applets de commande [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) et [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) pour naviguer dans les images. Vous déterminez ces valeurs :

1. en répertoriant les éditeurs d’images ;
2. pour un éditeur donné, en répertoriant ses offres ;
3. pour une offre donnée, en répertoriant ses références SKU.

Tout d’abord, répertoriez les serveurs de publication avec les commandes suivantes :

```azurepowershell-interactive
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Indiquez le nom d’éditeur de publication choisi et exécutez les commandes suivantes :

```azurepowershell-interactive
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Indiquez le nom de l’offre choisi et exécutez les commandes suivantes :

```azurepowershell-interactive
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

La sortie de la commande `Get-AzureRMVMImageSku` comprend toutes les informations dont vous avez besoin pour spécifier l’image pour une nouvelle machine virtuelle.

Vous trouverez ci-dessous un exemple complet :

```azurepowershell-interactive
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Output:

```
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

```azurepowershell-interactive
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Output:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

Pour l'offre « WindowsServer » :

```azurepowershell-interactive
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Output:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Nano-Server
```

Dans cette liste, copiez le nom de référence SKU choisi. Vous disposez de toutes les informations pour l’applet de commande PowerShell `Set-AzureRMVMSourceImage` ou pour un modèle de groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant choisir précisément l'image que vous voulez utiliser. Pour créer rapidement une machine virtuelle en utilisant les informations d’image que vous venez de trouver, consultez [Créer une machine virtuelle Windows avec PowerShell](quick-create-powershell.md).

