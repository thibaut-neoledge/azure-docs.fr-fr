<properties
	pageTitle="Création de jeux de mise à l’échelle de machine virtuelle à l’aide des applets de commande PowerShell | Microsoft Azure"
	description="Prise en main de la création et de la gestion de vos premiers jeux de mise à l’échelle de machine virtuelle Azure à l’aide des applets de commande Azure PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="danielsollondon"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="danielsollondon"/>

# Création de jeux de mise à l’échelle de machine virtuelle à l’aide des applets de commande PowerShell

Il s’agit d’un exemple illustrant comment créer un jeu de mise à l’échelle de machine virtuelle (VMSS). Cet exemple crée un VMSS de 3 nœuds, avec la mise en réseau et le stockage associés.

## Premières étapes
Assurez-vous que le dernier module Azure PowerShell est installé. Celui-ci contient les applets de commande PowerShell nécessaires pour mettre à jour et créer le VMSS. Accédez aux outils de ligne de commande [ici](http://aka.ms/webpi-azps) pour obtenir les derniers modules Azure disponibles.

Pour trouver les applets de commande pour le VMSS, utilisez la chaîne de recherche *VMSS*.

## Création d’un jeu de mise à l’échelle de machine virtuelle (VMSS)

##### Créer un groupe de ressources

```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

##### Créer un compte de stockage

Définissez le type et le nom du compte de stockage.

```
$stoname = 'sto' + $rgname;
$stotype = 'Standard_LRS';
 New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname -Location $loc -SkuName $stotype -Kind "Storage";

$stoaccount = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname;
```

#### Créer la mise en réseau (réseau virtuel / sous-réseau)

##### Spécification du sous-réseau

```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

##### Spécification du réseau virtuel (VNET)

```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -DnsServer "10.1.1.1" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

#In this case below we assume the new subnet is the only one, note difference if you have one already or have adjusted this code to more than one subnet.
$subnetId = $vnet.Subnets[0].Id;
```

##### Créer la ressource IP publique pour permettre l’accès externe

Celle-ci est liée à l’équilibrage de charge.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

##### Créer et configurer l’équilibrage de charge

```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

#Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

##### Configurer l’équilibrage de charge
Créez la configuration du pool d’adresses principales. Celle-ci est partagée par les cartes réseau des machines virtuelles dans le VMSS.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Définissez le port de sonde de l’équilibrage de charge, modifiez les paramètres en fonction de votre application.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Créez des règles NAT pour la connectivité directe routée (sans équilibrage de charge) aux machines virtuelles dans le VMSS via l’équilibreur de charge. Notez que cet exemple illustre l’utilisation de RDP. Il s’agit simplement d’une démonstration et les méthodes VNET internes doivent être utilisées pour la création d’une liaison RDP à ces serveurs.

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

Vérifiez les paramètres de l’équilibrage de charge, vérifiez les configurations de l’équilibrage de charge du port. Notez que vous ne verrez pas les règles NAT entrantes avant que la machine virtuelle dans le VMSS ne soit créée.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### Configurer et créer le jeu de mise à l’échelle de machine virtuelle (VMSS)

Notez que cet exemple d’infrastructure illustre comment configurer, distribuer et mettre à l’échelle le trafic web sur le VMSS, mais aucun service web n’est installé sur les images des machines virtuelles spécifiées ici.

```
#specify VMSS Name
$vmssName = 'vmss' + $rgname;

##specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

$vhdContainer = "https://" + $stoname + ".blob.core.windows.net/" + $vmssName;

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

$ipCfg.LoadBalancerBackendAddressPools.Add($actualLb.BackendAddressPools[0].Id);
$ipCfg.LoadBalancerInboundNatPools.Add($actualLb.InboundNatPools[0].Id);
```

Créer la configuration du VMSS

```
#Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -Name 'test' -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName -VhdContainer $vhdContainer `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Générer la configuration du VMSS

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Maintenant, le VMSS est créé. Vous pouvez tester la connexion à la machine virtuelle individuelle à l’aide de RDP dans cet exemple :

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```

<!---HONumber=AcomDC_0824_2016-->