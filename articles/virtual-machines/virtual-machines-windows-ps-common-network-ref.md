---
title: "Commandes réseau Azure PowerShell courantes pour les machines virtuelles | Microsoft Docs"
description: "Il s’agit de commandes PowerShell courantes, qui vous aident à démarrer la création d’un réseau virtuel et des ressources associées pour des machines virtuelles."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: ce08e9dcd0585e00bdd42b8aa34ac2c597a53a6e


---
# <a name="common-network-azure-powershell-commands-for-vms"></a>Commandes Azure PowerShell courantes pour les réseaux des machines virtuelles
Pour créer une machine virtuelle, vous devez d’abord créer un [réseau virtuel](../virtual-network/virtual-networks-overview.md) ou identifier un réseau virtuel existant, au sein duquel la machine virtuelle peut être ajoutée. En général, lorsque vous créez une machine virtuelle, vous devez également envisager la création des ressources décrites dans cet article.

Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="create-network-resources"></a>Créer des ressources réseau
| Task | Commande |
| --- | --- |
| Créez des configurations de sous-réseau |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -Name "subnet_name" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "subnet_name" -AddressPrefix XX.X.X.X/XX<BR><BR>Un réseau classique peut avoir un sous-réseau pour un [équilibrage de charge accessible sur Internet](../load-balancer/load-balancer-internet-overview.md) et un sous-réseau distinct pour un [équilibrage de charge interne](../load-balancer/load-balancer-internal-overview.md). |
| Créez un réseau virtuel |$vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -Name "virtual_network_name" -ResourceGroupName "resource_group_name" -Location "location_name" -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Test d’un nom de domaine unique |[Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) -DomainQualifiedName "domain_name" -Location "location_name"<BR><BR>Vous pouvez spécifier un nom de domaine DNS pour une [ressource IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md), qui crée un mappage pour l’élément domainname.location.cloudapp.azure.com vers l’adresse IP publique dans les serveurs DNS gérés par Azure. Le nom ne peut contenir que des lettres, des chiffres et des traits d’union. Le premier et le dernier caractère doivent correspondre à une lettre ou à un chiffre, et le nom de domaine doit être unique au sein de son emplacement Azure. Si la valeur **True** est renvoyée, cela signifie que le nom proposé est bien unique au niveau global. |
| Créer une adresse IP publique |$pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -Name "ip_address_name" -ResourceGroupName "resource_group_name" -DomainNameLabel "domain_name" -Location "location_name" -AllocationMethod Dynamic<BR><BR>L’adresse IP publique utilise le nom de domaine que vous avez testé. Elle est utilisée par la configuration de serveur frontal de l’équilibrage de charge. |
| Créer une configuration d’adresse IP frontale |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -Name "frontend_ip_name" -PublicIpAddress $pip<BR><BR>La configuration du serveur frontal inclut l’adresse IP publique que vous avez créée pour le trafic réseau entrant. |
| Créer un pool d’adresses principal |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -Name "backend_pool_name"<BR><BR>Fournit des adresses internes pour le serveur principal de l’équilibreur de charge, accessibles via une interface réseau. |
| Créer une sonde |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -Name "probe_name" -RequestPath ’HealthProbe.aspx’ -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machine virtuelle dans le pool d’adresses du serveur principal. |
| Créer une règle d’équilibrage de charge |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Contient des règles qui attribuent un port public de l’équilibrage de charge à un port du pool d’adresses principal. |
| Créer une règle NAT entrante |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -Name "rule_name" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Contient des règles qui mappent un port public de l’équilibreur de charge à un port associé à une machine virtuelle spécifique dans le pool d’adresses du serveur principal. |
| Créer un équilibrage de charge |$loadBalancer = [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) -ResourceGroupName "resource_group_name" -Name "load_balancer_name" -Location "location_name" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Créer une interface réseau |$nic1= [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) -ResourceGroupName "resource_group_name" -Name "network_interface_name" -Location "location_name" -PrivateIpAddress XX.X.X.X -Subnet subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crée une interface réseau via l’adresse IP publique et le sous-réseau associé à un réseau virtuel que vous avez créé. |

## <a name="get-information-about-network-resources"></a>Obtenir des informations sur les ressources réseau
| Task | Commande |
| --- | --- |
| Répertorier les réseaux virtuels |[Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) -ResourceGroupName "resource_group_name"<BR><BR>Répertorie tous les réseaux virtuels du groupe de ressources. |
| Obtenir des informations sur un réseau virtuel |Get-AzureRmVirtualNetwork -Name "virtual_network_name" -ResourceGroupName "resource_group_name" |
| Répertorier les sous-réseaux d’un réseau virtuel |Get-AzureRmVirtualNetwork -Name "virtual_network_name" -ResourceGroupName "resource_group_name" &#124; Select Subnets |
| Obtenir des informations sur un sous-réseau |[Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -Name "subnet_name" -VirtualNetwork $vnet<BR><BR>Obtient des informations sur le sous-réseau du réseau virtuel spécifié. La valeur $vnet représente l’objet renvoyé par Get-AzureRmVirtualNetwork. |
| Répertorier des adresses IP |[Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) -ResourceGroupName "resource_group_name"<BR><BR>Répertorie les adresses IP publiques du groupe de ressources. |
| Répertorier les équilibrages de charge |[Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) -ResourceGroupName "resource_group_name"<BR><BR>Répertorie tous les équilibrages de charge du groupe de ressources. |
| Répertorier les interfaces réseau |[Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) -ResourceGroupName "resource_group_name"<BR><BR>Répertorie toutes les interfaces réseau du groupe de ressources. |
| Obtenir des informations sur une interface réseau |Get-AzureRmNetworkInterface -Name "network_interface_name" -ResourceGroupName "resource_group_name"<BR><BR>Obtient des informations sur une interface réseau spécifique. |
| Obtenir la configuration IP d’une interface réseau |[Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -Name "ipconfiguration_name" -NetworkInterface $nic<BR><BR>Obtient des informations sur la configuration IP de l’interface réseau spécifiée. La valeur $nic représente l’objet renvoyé par Get-AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Gérer des ressources réseau
| Task | Commande |
| --- | --- |
| Ajouter un sous-réseau à un réseau virtuel |[Add-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) -AddressPrefix XX.X.X.X/XX -Name "subnet_name" -VirtualNetwork $vnet<BR><BR>Ajoute un sous-réseau à un réseau virtuel existant. La valeur $vnet représente l’objet renvoyé par Get-AzureRmVirtualNetwork. |
| Supprimer un réseau virtuel |[Remove-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -Name "virtual_network_name" -ResourceGroupName "resource_group_name"<BR><BR>Supprime le réseau virtuel spécifié du groupe de ressources. |
| Supprimer une interface réseau |[Remove-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -Name "network_interface_name" -ResourceGroupName "resource_group_name"<BR><BR>Supprime l’interface réseau spécifiée du groupe de ressources. |
| Suppression d’un équilibreur de charge |[Remove-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -Name "load_balancer_name" -ResourceGroupName "resource_group_name"<BR><BR>Supprime l’équilibrage de charge spécifié du groupe de ressources. |
| Supprimer une adresse IP publique |[Remove-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-Name "ip_address_name" -ResourceGroupName "resource_group_name"<BR><BR>Supprime l’adresse IP publique spécifiée du groupe de ressources. |

## <a name="next-steps"></a>Étapes suivantes
* Utilisez l’interface réseau que vous avez créée en même temps que [la machine virtuelle](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Découvrez comment [créer une machine virtuelle avec plusieurs interfaces réseau](../virtual-network/virtual-networks-multiple-nics.md).




<!--HONumber=Feb17_HO2-->


