---
title: Commandes réseau Azure PowerShell courantes pour les machines virtuelles | Microsoft Docs
description: Il s’agit de commandes PowerShell courantes, qui vous aident à démarrer la création d’un réseau virtuel et des ressources associées pour des machines virtuelles.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2016
ms.author: davidmu

---
# Commandes Azure PowerShell courantes pour les réseaux des machines virtuelles
Pour créer une machine virtuelle, vous devez d’abord créer un [réseau virtuel](../virtual-network/virtual-networks-overview.md) ou identifier un réseau virtuel existant, au sein duquel la machine virtuelle peut être ajoutée. En général, lorsque vous créez une machine virtuelle, vous devez également envisager la création des ressources décrites dans cet article.

## Création de ressources via Azure PowerShell
Consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour savoir comment installer la dernière version d’Azure PowerShell, sélectionner l’abonnement à utiliser et vous connecter à votre compte Azure.

| Ressource | Commande |
| --- | --- |
| Sous-réseau |$internetSubnet = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name internetSubnet -AddressPrefix 10.0.1.0/24<BR>$internalSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name internalSubnet -AddressPrefix 10.0.2.0/24<BR><BR>Un réseau classique peut présenter un sous-réseau pour un [équilibreur de charge accessible sur Internet](../load-balancer/load-balancer-internet-overview.md) et un sous-réseau distinct pour un [équilibreur de charge interne](../load-balancer/load-balancer-internal-overview.md). |
| Réseau virtuel |Création d’un réseau virtuel :<BR><BR>$rgName = "[resource-group-name](../powershell-azure-resource-manager.md)"<BR>$locName = "[location-name](https://msdn.microsoft.com/library/azure/dn495177.aspx)"<BR>$vnetName = "virtual-network-name"<BR>$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $internetSubnet,$internalSubnet<BR><BR>Obtention d’une liste des réseaux virtuels :<BR><BR>[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName $rgName &#124; Sort Name &#124; Select Name<BR><BR>Création de la liste des sous-réseaux d’un réseau virtuel :<BR><BR>Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName &#124; Select Subnets<BR><BR>Vous devez obtenir un résultat similaire à ce qui suit, qui répertorie les sous-réseaux : <BR><BR>Sous-réseaux<BR>-------<BR>{internetSubnet, internalSubnet}<BR><BR>L’index de sous-réseau pour l’élément internetSubnet correspond à la valeur 0, alors que celui de l’élément internalSubnet correspond à la valeur 1. |
| Étiquette de nom de domaine |$domName = "domain-name"<BR>[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName $domName -Location $locName<BR><BR>Vous pouvez spécifier une étiquette de nom de domaine DNS pour une [ressource IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md), qui crée un mappage pour l’élément domainnamelabel.location.cloudapp.azure.com vers l’adresse IP publique dans les serveurs DNS gérés par Azure. L'étiquette ne peut contenir que des lettres, des chiffres et des traits d'union. Le premier et le dernier caractère doivent correspondre à une lettre ou à un chiffre, et l’étiquette de nom de domaine doit être unique au sein de son emplacement Azure. Vous devez toujours vérifier si l’étiquette de nom de domaine est unique au niveau global. Si la valeur **True** est renvoyée, cela signifie que le nom proposé est bien unique au niveau global. |
| Adresse IP publique |$ipName = "public-ip-address-name"<BR>$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name $ipName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic<BR><BR>L’adresse IP publique utilise l’étiquette de nom de domaine que vous avez créée. Elle est utilisée par la configuration du serveur frontal de l’équilibreur de charge. |
| Configuration d’adresses IP frontales |$feConfigName = "frontend-ip-config-name"<BR>$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name $feConfigName -PublicIpAddress $pip<BR><BR>La configuration du serveur frontal inclut l’adresse IP publique que vous avez créée pour le trafic réseau entrant. |
| Pool d’adresses principales |$bePoolName = "back-end-pool-name"<BR>$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name $bePoolName<BR><BR>Fournit des adresses internes pour le serveur principal de l’équilibreur de charge, accessibles via une interface réseau. |
| Sonde |$probeName = "health-probe-name"<BR>$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name $probeName -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machine virtuelle dans le pool d’adresses du serveur principal. |
| Règle d’équilibrage de la charge |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contient des règles qui mappent un port public de l’équilibreur de charge à un port du pool d’adresses du serveur principal. |
| Règle NAT entrante |$ruleName = "NAT-rule-name"<BR>$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name $ruleName -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contient des règles qui mappent un port public de l’équilibreur de charge à un port associé à une machine virtuelle spécifique dans le pool d’adresses du serveur principal. |
| Équilibrage de charge |$lbName = "load-balancer-name"<BR>$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName $rgName -Name $lbName -Location $locName -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Interface réseau |$vnet = [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -Name $vnetName -ResourceGroupName $rgName<BR>$internalSubnet = [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "internalSubnet" -VirtualNetwork $vnet<BR>$nicName = "network-interface-name"<BR>$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName $rgName -Name $nicName -Location $locName -PrivateIpAddress 10.0.2.6 -Subnet $internalSubnet -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crée une interface réseau via l’adresse IP publique et le sous-réseau associé à un réseau virtuel que vous avez créé. |

## Étapes suivantes
* Utilisez l’interface réseau que vous avez créée en même temps que [la machine virtuelle](virtual-machines-windows-ps-create.md).
* Découvrez comment [créer une machine virtuelle avec plusieurs interfaces réseau](../virtual-network/virtual-networks-multiple-nics.md).

<!---HONumber=AcomDC_0629_2016-->