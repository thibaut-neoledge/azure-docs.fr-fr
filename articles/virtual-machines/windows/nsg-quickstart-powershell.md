---
title: "Ouvrir des ports sur une machine virtuelle à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Découvrez comment ouvrir un port / créer un point de terminaison sur votre machine virtuelle Windows à l’aide du modèle de déploiement Azure Resource Manager et d’Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 0168bbc466f80c8603dda46ded56b7524e4e5e91
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Guide d’ouverture de ports et de points de terminaison sur une machine virtuelle dans Azure à l’aide de PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Commandes rapides
Pour créer un groupe de sécurité réseau et des règles de liste de contrôle d’accès, vous devez installer [la version la plus récente d’Azure PowerShell](/powershell/azureps-cmdlets-docs). Vous pouvez également [effectuer ces étapes à l’aide du Portail Azure](nsg-quickstart-portal.md).

Connectez-vous à votre compte Azure :

```powershell
Login-AzureRmAccount
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Les exemples de noms de paramètre comprennent *myResourceGroup*, *myNetworkSecurityGroup* et *myVMnet*.

Créez une règle avec [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). L’exemple suivant crée une règle nommée *myNetworkSecurityGroupRule* pour autoriser le trafic *TCP* sur le port *80* :

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Ensuite, créez votre groupe de sécurité réseau avec [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) et attribuez la règle HTTP que vous venez de créer comme suit. L’exemple suivant crée un groupe de sécurité réseau nommé *myNetworkSecurityGroup* :

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Maintenant, il s’agit d’affecter votre groupe de sécurité réseau à un sous-réseau. L’exemple suivant affecte un réseau virtuel existant nommé *myVnet* à la variable *$vnet* avec [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) :

```powershell
$vnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associez votre groupe de sécurité réseau à votre sous-réseau avec [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig). L’exemple suivant associe le sous-réseau nommé *mySubnet* à votre groupe de sécurité réseau :

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Enfin, mettez à jour votre réseau virtuel avec [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) pour que vos modifications prennent effet :

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Vous pouvez définir des groupes de sécurité réseau et des règles de liste de contrôle d’accès dans le cadre de modèles Azure Resource Manager. En savoir plus sur la [création de groupes de sécurité réseau avec des modèles](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser le réacheminement de port pour mapper un seul port externe sur un port interne de votre machine virtuelle, utilisez un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez peut-être exposer le port TCP 8080 en externe et diriger le trafic vers le port TCP 80 sur une machine virtuelle. En savoir plus sur [la création d'un équilibreur de charge accessible sur Internet](../../load-balancer/load-balancer-get-started-internet-arm-ps.md)

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

* [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Présentation du groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md)
* [Présentation d’Azure Resource Manager](../../load-balancer/load-balancer-arm.md)


