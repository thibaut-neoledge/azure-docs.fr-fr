---
title: "Création de jeux de mise à l’échelle de machine virtuelle à l’aide des applets de commande PowerShell | Microsoft Docs"
description: "Prise en main de la création et de la gestion de vos premiers jeux de mise à l’échelle de machine virtuelle Azure à l’aide des applets de commande Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 430d9d64-1f35-48f0-a4fd-9b69910ffa59
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: danielsollondon
translationtype: Human Translation
ms.sourcegitcommit: b2e89f0d5e6b14ce8d5847f8adc3d57a6122172e
ms.openlocfilehash: a3a36028a75d6cb7eb36277f3e2b5ab833c96a96
ms.lasthandoff: 02/21/2017


---
# <a name="creating-virtual-machine-scale-sets-using-powershell-cmdlets"></a>Création de jeux de mise à l’échelle de machine virtuelle à l’aide des applets de commande PowerShell
Cet article présente un exemple montrant comment créer un jeu de mise à l’échelle de machine virtuelle. Il crée un jeu de mise à l’échelle de trois nœuds, avec la mise en réseau et le stockage associés.

## <a name="first-steps"></a>Premières étapes
Assurez-vous que le dernier module Azure PowerShell est installé, pour vérifier que vous disposez des applets de commande PowerShell nécessaires pour mettre à jour et créer les jeux de mise à l’échelle.
Accédez aux outils en ligne de commande [ici](http://aka.ms/webpi-azps) pour obtenir les derniers modules Azure disponibles.

Pour trouver les applets de commande pour le VMSS, utilisez la chaîne de recherche \*VMSS\*. Par exemple, _gcm *vmss*_

## <a name="creating-a-vmss"></a>Création d’un jeu de mise à l’échelle de machine virtuelle (VMSS)
#### <a name="create-resource-group"></a>Créer un groupe de ressources
```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

### <a name="create-networking-vnet--subnet"></a>Créer la mise en réseau (réseau virtuel / sous-réseau)
#### <a name="subnet-specification"></a>Spécification du sous-réseau
```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

#### <a name="vnet-specification"></a>Spécification du réseau virtuel (VNET)
```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

# In this case assume the new subnet is the only one
$subnetId = $vnet.Subnets[0].Id;
```

#### <a name="create-public-ip-resource-to-allow-external-access"></a>Créer la ressource IP publique pour permettre l’accès externe
Celle-ci est liée à l’équilibrage de charge.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

#### <a name="create-load-balancer"></a>Créer un équilibreur de charge
```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

# Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

#### <a name="configure-load-balancer"></a>Configurer l’équilibrage de charge
Créez la configuration du pool d’adresses principales. Celle-ci est partagée par les cartes réseau des machines virtuelles dans le jeu de mise à l’échelle.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Définissez le port de sonde de l’équilibrage de charge, modifiez les paramètres en fonction de votre application.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Créez un pool NAT entrant pour une connectivité directe (sans équilibreur de charge) acheminé vers les machines virtuelles dans le jeu de mise à l’échelle via l’équilibreur de charge. Nous cherchons à montrer cela avec RDP, ce qui peut ne pas être nécessaire dans votre application.

```
$frontendpoolrangestart = 3360
$frontendpoolrangeend = 3370
$backendvmport = 3389
$inboundNatPool = New-AzureRmLoadBalancerInboundNatPoolConfig -Name $inboundNatPoolName -FrontendIPConfigurationId `
$frontend.Id -Protocol Tcp -FrontendPortRangeStart $frontendpoolrangestart -FrontendPortRangeEnd $frontendpoolrangeend -BackendPort $backendvmport;
```

Créez la règle d’équilibrage de charge, cet exemple illustre l’équilibrage de charge des requêtes sur le port 80, avec les paramètres des étapes précédentes.

```
$protocol = 'Tcp'
$feLBPort = 80
$beLBPort = 80

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name $lbruleName `
-FrontendIPConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -Protocol $protocol -FrontendPort $feLBPort -BackendPort $beLBPort `
-IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -Verbose;
```

Créez l’équilibrage de charge avec la configuration.

```
$actualLb = New-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname -Location $loc `
-FrontendIpConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -LoadBalancingRule $lbrule -InboundNatPool $inboundNatPool -Verbose;
```

Vérifiez les paramètres de l’équilibrage de charge, vérifiez les configurations de l’équilibrage de charge du port. Notez que vous ne verrez pas les règles NAT entrantes avant que la machine virtuelle dans le jeu de mise à l’échelle ne soit créée.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### <a name="configure-and-create-the-scale-set"></a>Configurer et créer le jeu de mise à l’échelle
Notez que cet exemple d’infrastructure illustre comment configurer, distribuer et mettre à l’échelle le trafic web sur le jeu de mise à l'échelle, mais aucun service web n’est installé sur les images des machines virtuelles spécifiées ici.

```
# specify scale set Name
$vmssName = 'vmss' + $rgname;

## specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

###add an extension
$extname = 'BGInfo';
$publisher = 'Microsoft.Compute';
$exttype = 'BGInfo';
$extver = '2.1';
```

Lier la carte réseau à l’équilibrage de charge et au sous-réseau

```
$ipCfg = New-AzureRmVmssIPConfig -Name 'nic' `
-LoadBalancerInboundNatPoolsId $actualLb.InboundNatPools[0].Id `
-LoadBalancerBackendAddressPoolsId $actualLb.BackendAddressPools[0].Id `
-SubnetId $subnetId;
```

Créer la configuration du jeu de mise à l’échelle

```
# Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Définir la configuration du jeu de mise à l’échelle

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Maintenant, le jeu de mise à l’échelle est créé. Vous pouvez tester la connexion à la machine virtuelle individuelle à l’aide de RDP dans cet exemple :

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```

