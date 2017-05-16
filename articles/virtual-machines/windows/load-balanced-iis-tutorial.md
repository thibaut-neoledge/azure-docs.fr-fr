---
title: "Didacticiel : Création d’une application hautement disponible sur des machines virtuelles Azure | Microsoft Docs"
description: "Apprenez à créer une application à haute disponibilité et sécurisée entre trois machines virtuelles Windows avec un équilibreur de charge dans Azure."
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
ms.date: 03/30/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 61fd0dc45cfa2d320713819be3db811be58ab77e
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-windows-virtual-machines-in-azure"></a>Création d’une application à haute disponibilité avec équilibrage de charge sur des machines virtuelles Windows dans Azure

Dans ce didacticiel, vous créez une application hautement disponible qui résiste aux événements de maintenance. L’application utilise trois machines virtuelles Windows, un groupe à haute disponibilité et un équilibreur de charge. Ce didacticiel installe IIS, mais vous pouvez l’utiliser pour déployer une infrastructure d’application utilisant les mêmes instructions et composants à haute disponibilité. 

## <a name="step-1---azure-prerequisites"></a>Étape 1 : Conditions préalables pour Azure

Pour suivre ce didacticiel, assurez-vous que vous avez installé le dernier module [d’Azure PowerShell](/powershell/azure/overview).

Pour commencer, connectez-vous à votre abonnement Azure avec la commande Login-AzureRmAccount et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Vous devez créer un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) pour pouvoir créer d’autres ressources Azure. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans la région `westeurope` : 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-2---create-availability-set"></a>Étape 2 : Créer un groupe à haute disponibilité

Les machines virtuelles peuvent être créées sur les domaines de mise à jour et d’erreur logiques. Chaque domaine logique représente une partie du matériel dans le centre de données Azure sous-jacent. Lorsque vous créez deux ou plusieurs machines virtuelles, vos ressources de calcul et de stockage sont réparties sur ces domaines. Cette distribution assure la disponibilité de votre application si un composant matériel nécessite une maintenance. Les groupes à haute disponibilité permettent de définir ces domaines d’erreur et de mise à jour logiques.

Créez un groupe à haute disponibilité avec la commande [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). L’exemple suivant permet de créer un groupe à haute disponibilité nommé `myAvailabilitySet` :

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroup `
  -Name myAvailabilitySet `
  -Location westeurope `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

## <a name="step-3---create-load-balancer"></a>Étape 3 : Créer un équilibreur de charge

Un équilibrage de charge Azure répartit le trafic sur un ensemble de machines virtuelles définies à l’aide de règles d’équilibrage de charge. Une sonde d’intégrité surveille un port donné sur chaque machine virtuelle et ne distribue le trafic que vers une machine virtuelle opérationnelle.

### <a name="create-public-ip-address"></a>Créer une adresse IP publique

Pour accéder à votre application sur Internet, affectez une adresse IP publique à l’équilibrage de charge. Créez une adresse IP publique avec [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). L’exemple suivant crée une adresse IP publique nommée `myPublicIP` :

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

Créez une adresse IP frontale avec [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). L’exemple suivant crée une adresse IP frontale nommée `myFrontEndPool` : 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name myFrontEndPool -PublicIpAddress $pip
```

Créez un pool d’adresses principales avec [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). L’exemple suivant permet de créer un pool d’adresses principales nommé `myBackEndPool` :

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Maintenant, créez l’équilibreur de charge avec [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). L’exemple suivant permet de créer un équilibrage de charge nommé `myLoadBalancer` avec l’adresse `myPublicIP` :

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroup `
  -Name myLoadBalancer `
  -Location westeurope `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-health-probe"></a>Créer une sonde d’intégrité

Pour permettre à l’équilibrage de charge de surveiller l’état de votre application, vous utilisez une sonde d’intégrité. La sonde d’intégrité ajoute ou supprime dynamiquement des machines virtuelles de la rotation d’équilibrage de charge en fonction de leur réponse aux vérifications d’intégrité. Par défaut, une machine virtuelle est supprimée de la distribution d’équilibrage de charge après deux échecs consécutifs à des intervalles de 15 secondes.

Créez une sonde d’intégrité avec [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). L’exemple suivant permet de créer une sonde d’intégrité nommée `myHealthProbe` qui surveille chaque machine virtuelle :

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles.

Créez une règle d’équilibreur de charge avec [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). L’exemple suivant permet de créer une règle d’équilibreur de charge nommée `myLoadBalancerRule` et d’équilibrer le trafic sur le port `80` :

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

Mettez à jour l’équilibreur de charge avec [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) :

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="step-4---configure-networking"></a>Étape 4 - Configuration de la mise en réseau

Chaque machine virtuelle possède une ou plusieurs cartes d’interface réseau (NIC) virtuelles qui se connectent à un réseau virtuel. Ce réseau virtuel est sécurisé pour filtrer le trafic en fonction des règles d’accès définies.

### <a name="create-virtual-network"></a>Création d’un réseau virtuel

Tout d’abord, configurez un sous-réseau avec [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L’exemple suivant permet de créer un sous-réseau nommé `mySubnet` :

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

Pour fournir la connectivité réseau à vos machines virtuelles, créez un réseau virtuel avec [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L’exemple suivant crée un réseau virtuel nommé `myVnet` avec `mySubnet` :

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="configure-network-security"></a>Configurer la sécurité réseau

Un [groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md) (NSG) Azure contrôle le trafic entrant et sortant pour une ou plusieurs machines virtuelles. Les règles de groupe de sécurité réseau autorisent ou refusent le trafic réseau sur un port ou une plage de ports spécifique. Ces règles peuvent également inclure un préfixe d’adresse source afin que seul le trafic provenant d’une source prédéfinie puisse communiquer avec une machine virtuelle.

Pour permettre au trafic web d’atteindre votre application, créez une règle de groupe de sécurité réseau avec [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). L’exemple suivant permet de créer une règle de groupe de sécurité réseau nommée `myNetworkSecurityGroupRule` :

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Créez un groupe de sécurité réseau avec [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). L’exemple suivant permet de créer un groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

Ajoutez le groupe de sécurité réseau au sous-réseau avec [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) :

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

Mettez à jour le réseau virtuel avec [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) :

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-network-interface-cards"></a>Création de cartes d’interface réseau virtuelles

Les équilibrages de charge fonctionnent avec la ressource de carte réseau virtuelle plutôt que la machine virtuelle elle-même. La carte réseau virtuelle est connectée à l’équilibrage de charge, puis attachée à une machine virtuelle.

Créez une carte réseau virtuelle avec [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). L’exemple suivant crée trois cartes réseau virtuelles. (Une carte réseau virtuelle pour chaque machine virtuelle que vous créez pour votre application dans les étapes suivantes) :


```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroup `
     -Name myNic$i `
     -Location westeurope `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}

```

## <a name="step-5---create-virtual-machines"></a>Étape 5 : Créer les machines virtuelles

Tous les composants sous-jacents étant en place, vous pouvez désormais créer des machines virtuelles hautement disponibles pour exécuter votre application. 

Récupérez le nom d’utilisateur et le mot de passe du compte d’administrateur sur la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```powershell
$cred = Get-Credential
```

Créez les machines virtuelles avec [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) et [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant crée trois machines virtuelles :

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -StorageAccountType StandardLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
}

```

La création et la configuration des trois machines virtuelles prennent quelques minutes. La sonde d’intégrité de l’équilibrage de charge détecte automatiquement lorsque l’application est en cours d’exécution sur chaque machine virtuelle. Une fois que l’application est en cours d’exécution, la règle d’équilibrage de charge commence à distribuer le trafic.

### <a name="install-the-app"></a>Installer l’application 

Les extensions de machines virtuelles Azure sont utilisées pour automatiser les tâches de configuration de machines virtuelles telles que l’installation d’applications et la configuration du système d’exploitation. [L’extension de script personnalisé pour Windows](./../virtual-machines-windows-extensions-customscript.md) permet d’exécuter n’importe quel script PowerShell sur la machine virtuelle. Le script peut être stocké dans le stockage Azure, dans tout point de terminaison HTTP accessible, ou il peut être intégré dans la configuration d’extension de script personnalisé. Si l’extension de script personnalisé est utilisée, l’agent de machine virtuelle Azure gère l’exécution du script.

Utilisez [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) pour installer l’extension de script personnalisé. L’extension exécute `powershell Add-WindowsFeature Web-Server` pour installer le serveur web IIS :

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
     -Location westeurope
}
```

### <a name="test-your-app"></a>Test de l'application

Obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP de `myPublicIP` créée précédemment :

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIP | select IpAddress
```

Entrez l’adresse IP publique dans un navigateur web. Lorsque la règle de groupe de sécurité réseau est mise en place, le site web IIS par défaut s’affiche. 

![Site IIS par défaut](media/load-balanced-iis-tutorial/iis.png)

## <a name="step-6--management-tasks"></a>Étape 6 : Tâches de gestion

Vous devrez peut-être effectuer la maintenance sur la machine virtuelle exécutant votre application, avec par exemple l’installation des mises à jour du système d’exploitation. Pour faire face à une augmentation du trafic vers votre application, vous devrez peut-être ajouter des machines virtuelles supplémentaires. Cette section vous indique comment supprimer ou ajouter une machine virtuelle pour l’équilibrage de charge. 

### <a name="remove-a-vm-from-the-load-balancer"></a>Supprimer une machine virtuelle de l’équilibrage de charge

Supprimez une machine virtuelle du pool d’adresses principales en réinitialisant la propriété LoadBalancerBackendAddressPools de la carte d’interface réseau.

Récupérez la carte d’interface réseau avec [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) :

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic2
``` 

Définissez la propriété LoadBalancerBackendAddressPools de la carte d’interface réseau sur $null :

```powershell
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
```

Mettez à jour la carte d’interface réseau :

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

### <a name="add-a-vm-to-the-load-balancer"></a>Ajouter une machine virtuelle à l’équilibrage de charge

Après avoir effectué les opérations de maintenance de la machine virtuelle, ou si vous avez besoin de développer la capacité, ajoutez la carte réseau d’une machine virtuelle au pool d’adresses principales de l’équilibreur de charge.

Récupérez l’équilibreur de charge :

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroup -Name myLoadBalancer 
```

Ajoutez le pool d’adresses principales de l’équilibreur de charge à la carte d’interface réseau :

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
```

Mettez à jour la carte d’interface réseau :

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Étapes suivantes

Exemples – [Exemples de scripts PowerShell pour Machines virtuelles Azure](./../virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

