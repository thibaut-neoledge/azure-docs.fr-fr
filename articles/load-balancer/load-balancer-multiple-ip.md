---
title: "Équilibrage de charge sur plusieurs configurations IP | Microsoft Docs"
description: "Équilibrage de charge sur des configurations IP principales et secondaires."
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 8761e32f22471e00e33605916fc145881c8fe378
ms.openlocfilehash: fed40bd97114a2f4eafedc5587b4744d73b2a0ff

---

# <a name="load-balancing-on-multiple-ip-configurations"></a>Équilibrage de charge sur plusieurs configurations IP

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [INTERFACE DE LIGNE DE COMMANDE](load-balancer-multiple-ip-cli.md)
>

Cet article décrit comment utiliser Azure Load Balancer avec plusieurs adresses IP par interface de réseau virtuel (carte réseau). La prise en charge de plusieurs adresses IP sur une carte réseau est une fonctionnalité actuellement disponible en version préliminaire. Pour plus d’informations, voir la section [Limitations](#limitations) de cet article. Le scénario suivant illustre le fonctionnement de cette fonctionnalité avec l’équilibrage de charge.

Pour ce scénario, nous avons deux machines virtuelles exécutant Windows, chacune avec une seule carte réseau. Chaque carte réseau possède plusieurs configurations IP. Chaque machine virtuelle héberge les deux sites web pour contoso.com et fabrikam.com. Chaque site web est lié à l’une des configurations IP sur la carte réseau. Nous utilisons un équilibrage de charge pour exposer deux adresses IP frontales, une par site web, afin de distribuer le trafic à la configuration IP correspondante pour le site web. Ce scénario utilise le même numéro de port sur les deux serveurs frontaux, ainsi que les deux adresses IP de pool principal.

![Image du scénario LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>Limitations

Actuellement, la configuration d’un équilibrage de charge sur des configurations IP secondaires n’est possible qu’en utilisant Azure PowerShell et Azure CLI. Cette limitation est temporaire et peut changer à tout moment. Revisitez sur cette page pour vérifier si elle contient des mises à jour.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Pour obtenir la version préliminaire, envoyez un e-mail à [Plusieurs adresses IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) en indiquant votre ID d’abonnement et l’utilisation prévue.

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Étapes pour équilibrer la charge sur plusieurs configurations IP

Pour accomplir le scénario décrit dans cet article, suivez les étapes ci-dessous :

1. Installez Azure PowerShell. Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
2. Créez un groupe de ressources à l’aide des paramètres suivants :

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Pour plus d’informations, voir l’Étape 2 de [Créer un groupe de ressources](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Créez un groupe à haute disponibilité](../virtual-machines/virtual-machines-windows-create-availability-set.md?toc=%2fazure%2fload-balancer%2ftoc.json) pour contenir vos machines virtuelles. Pour ce scénario, utilisez la commande suivante :

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Suivez les instructions des étapes 3 à 5 de l’article [Créer une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) pour préparer la création d’une machine virtuelle avec une seule carte réseau. Exécutez l’étape 6.1, puis procédez comme suit au lieu de suivre l’étape 6.2 :

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Accomplissez ensuite les étapes 6.3 à 6.8 de l’article [Créer une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

5. Ajoutez une deuxième configuration IP à chacune des machines virtuelles. Suivez les instructions de l’article [Affecter plusieurs adresses IP aux machines virtuelles](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add). Utilisez les paramètres de configuration suivants :

    ```powershell
    $NicName = "VM1-NIC"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Pour les besoins de ce didacticiel, il est inutile d’associer les configurations IP secondaires à des adresses IP publiques. Modifiez la commande pour supprimer la partie d’association d’adresse IP publique.

6. Exécutez à nouveau les étapes 4 à 6 de cet article pour la machine virtuelle VM2. Veillez à remplacer le nom de la machine virtuelle par VM2 lors de cette opération. Notez que vous n’avez pas besoin créer un réseau virtuel pour la deuxième machine virtuelle. Vous pouvez ou non créer un sous-réseau, selon votre cas d’utilisation.

7. Créez deux adresses IP publiques et stockez-les dans les variables appropriées, comme indiqué :

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Créez deux configurations IP frontales :

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Créer vos pools d’adresses principaux, une sonde et vos règles d’équilibrage de charge :

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Une fois les ressources créées, créez votre équilibreur de charge :

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Ajoutez le deuxième pool d’adresses principal et la configuration IP frontale à l’équilibrage de charge que vous venez de créer :

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Les commandes ci-dessous obtiennent les cartes réseau, puis ajoutent les deux configurations IP de chaque carte réseau au pool d’adresses principales de l’équilibrage de charge :

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. Enfin, vous devez configurer les enregistrements de ressource DNS pour qu’ils pointent vers l’adresse IP frontale correspondante de l’équilibrage de charge. Vous pouvez héberger vos domaines dans Azure DNS. Pour plus d’informations sur l’utilisation d’Azure DNS avec un équilibrage de charge, voir [Utiliser Azure DNS avec d’autres services Azure](../dns/dns-for-azure-services.md).



<!--HONumber=Nov16_HO5-->


