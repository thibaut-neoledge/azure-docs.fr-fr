---
title: "Réseaux virtuels Azure et machines virtuelles Windows | Microsoft Docs"
description: "Didacticiel : Gérer des réseaux virtuels Azure et des machines virtuelles Windows avec Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7fd0ace35cfe0286c874e4a75b733053aa945d39
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Gérer des réseaux virtuels Azure et des machines virtuelles Windows avec Azure PowerShell

Les machines virtuelles Azure utilisent la gestion réseau Azure pour la communication réseau interne et externe. Dans ce didacticiel, vous apprendrez à créer plusieurs machines virtuelles dans un réseau virtuel et à configurer leur connectivité réseau. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer des sous-réseaux de réseau virtuel
> * Contrôler le trafic réseau avec les groupes de sécurité réseau
> * Afficher les règles de trafic en action

Ce didacticiel requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-vnet"></a>Créer un réseau virtuel

Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Il s’agit d’un isolement logique du cloud Azure dédié à votre abonnement. Un réseau virtuel comporte des sous-réseaux, des règles de connectivité vers ces sous-réseaux, et des connexions entre les machines virtuelles et les sous-réseaux.

Vous devez créer un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) pour pouvoir créer d’autres ressources Azure. L’exemple suivant crée un groupe de ressources nommé *myRGNetwork* à l’emplacement *EastUS* :

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Un sous-réseau est une ressource enfant d’un réseau virtuel, et permet de définir des segments d’espaces d’adressage dans un bloc CIDR, à l’aide de préfixes d’adresses IP. Les cartes d’interface réseau (NIC) peuvent être ajoutées aux sous-réseaux et connectées aux machines virtuelles, ce qui fournit une connectivité pour différentes charges de travail.

Créez un sous-réseau avec [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) :

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Créez un réseau virtuel nommé *myVNet* en utilisant *myFrontendSubnet* avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) :

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>Créer une machine virtuelle frontale

Pour communiquer avec un réseau virtuel, une machine virtuelle a besoin d’une interface réseau virtuelle. *myFrontendVM* est accessible à partir d’Internet et a donc aussi besoin d’une adresse IP publique. 

Créez une adresse IP publique avec [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) :

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

Créez une carte réseau avec [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) :


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Définissez le nom d’utilisateur et le mot de passe pour le compte Administrateur sur la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```powershell
$cred = Get-Credential
```

Créez les machines virtuelles avec [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) et [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). 

```powershell
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="install-web-server"></a>Installer le serveur web

Vous pouvez installer IIS sur *myFrontendVM* à partir d’une session Bureau à distance. Vous avez besoin de l’adresse IP publique de la machine virtuelle pour y accéder.

Vous pouvez obtenir l’adresse IP publique de *myFrontendVM* avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP pour l’adresse *myPublicIPAddress* créée précédemment :

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myRGNetwork `
    -Name myPublicIPAddress | select IpAddress
```

Notez cette adresse IP : vous en aurez besoin lors des étapes suivantes.

Utilisez la commande suivante pour créer une session Bureau à distance avec *myFrontendVM*. Remplacez *<publicIPAddress>* par l’adresse que vous avez enregistrée précédemment. À l’invite, saisissez les informations d’identification que vous avez utilisées lors de la création de la machine virtuelle.

```
mstsc /v:<publicIpAddress>
``` 

Maintenant que vous êtes connecté à *myFrontendVM*, vous pouvez utiliser une seule ligne de PowerShell pour installer IIS et activer la règle de pare-feu local pour autoriser le trafic web. Ouvrez une invite PowerShell et exécutez la commande suivante :

Utilisez [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) pour exécuter l’extension de script personnalisé qui installe le serveur Web IIS :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Vous pouvez maintenant utiliser l’adresse IP publique pour accéder à la machine virtuelle et afficher le site IIS.

![Site IIS par défaut](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>Gérer le trafic interne

Un groupe de sécurité réseau contient une liste de règles de sécurité qui autorisent ou rejettent le trafic réseau vers les ressources connectées à un réseau virtuel. Les groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des cartes réseau distinctes qui sont attachées aux machines virtuelles. L’ouverture et la fermeture de l’accès aux machines virtuelles par le biais des ports s’effectuent à l’aide des règles de groupe de sécurité réseau. Quand vous avez créé *myFrontendVM*, le port d’entrée 3389 a été automatiquement ouvert pour la connectivité RDP.

La communication interne des machines virtuelles peut être configurée à l’aide d’un groupe de sécurité réseau. Dans cette section, vous allez apprendre à créer un sous-réseau supplémentaire dans le réseau et à lui affecter un groupe de sécurité réseau pour autoriser une connexion de *myFrontendVM* vers *myBackendVM* sur le port 1433. Le sous-réseau est ensuite affecté à la machine virtuelle lors de sa création.

Vous pouvez limiter le trafic interne vers *myBackendVM* en provenance uniquement de *myFrontendVM* en créant un groupe de sécurité réseau pour le sous-réseau back-end. L’exemple suivant crée une règle de groupe de sécurité réseau nommée *myBackendNSGRule* avec [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) :

```powershell
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Ajoutez un groupe de sécurité réseau nommé *myBackendNSG* avec [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) :

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>Ajouter un sous-réseau principal

Ajoutez *myBackEndSubnet* à *myVNet* avec [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) :

```powershell
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
```

## <a name="create-back-end-vm"></a>Créer une machine virtuelle principale

Pour créer une machine virtuelle principale, le plus simple consiste à utiliser une image SQL Server. Ce didacticiel crée la machine virtuelle avec le serveur de base de données, mais il ne fournit pas d’informations sur l’accès à la base de données.

Créez *myBackendNic* :

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Définissez le nom d’utilisateur et le mot de passe pour le compte Administrateur sur la machine virtuelle avec Get-Credential :

```powershell
$cred = Get-Credential
```

Créez *myBackendVM* :

```powershell
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016SP1-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

L’image utilisée inclut SQL Server, mais elle n’est pas utilisée dans ce didacticiel. Elle est présentée pour vous montrer comment configurer une machine virtuelle afin de gérer le trafic web, et comment configurer une machine virtuelle afin de traiter la gestion de la base de données.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé et sécurisé des réseaux Azure concernant les machines virtuelles. 

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer des sous-réseaux de réseau virtuel
> * Contrôler le trafic réseau avec les groupes de sécurité réseau
> * Afficher les règles de trafic en action

Passez au didacticiel suivant pour découvrir comment sécuriser et surveiller les données sur des machines virtuelles avec Sauvegarde Azure. .

> [!div class="nextstepaction"]
> [Sauvegarde de machines virtuelles Windows dans Azure](./tutorial-backup-vms.md)

