<properties
   pageTitle="Ouvrir des ports sur une machine virtuelle Azure à l’aide de PowerShell | Microsoft Azure"
   description="Découvrez comment ouvrir un port / créer un point de terminaison sur votre machine virtuelle Windows à l’aide du modèle de déploiement Azure Resource Manager et d’Azure PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="iainfou"/>

# Ouverture de ports sur une machine virtuelle dans Azure à l’aide de PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Commandes rapides
Pour créer un groupe de sécurité réseau et des règles de liste de contrôle d’accès, vous devez installer [la version la plus récente d’Azure PowerShell](../powershell-install-configure.md). Vous pouvez également [effectuer ces étapes à l’aide du Portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Tout d’abord, vous devez créer une règle pour autoriser le trafic HTTP sur le port TCP 80 en entrant votre propre nom et votre description :

```
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name http-rule -Description "Allow HTTP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Ensuite, créez votre groupe de sécurité réseau et attribuez la règle HTTP que vous venez de créer comme suit, en entrant le nom et l’emplacement de votre groupe de ressources :

```
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `
    -Name "TestNSG" -SecurityRules $httprule
```

Maintenant, il s’agit d’affecter votre groupe de sécurité réseau à un sous-réseau. Sélectionnez tout d’abord le réseau virtuel :

```
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
```

Associez votre groupe de sécurité réseau à votre sous-réseau :

```
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet `
    -NetworkSecurityGroup $nsg
```

Enfin, mettez à jour votre réseau virtuel pour que vos modifications prennent effet :

```
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Vous pouvez définir des groupes de sécurité réseau et des règles de liste de contrôle d’accès dans le cadre de modèles Azure Resource Manager. En savoir plus sur la [création de groupes de sécurité réseau avec des modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser le réacheminement de port pour mapper un seul port externe sur un port interne de votre machine virtuelle, utilisez un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez peut-être exposer le port TCP 8080 en externe et diriger le trafic vers le port TCP 80 sur une machine virtuelle. En savoir plus sur [la création d'un équilibreur de charge accessible sur Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md)

## Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

- [Présentation d’Azure Resource Manager](../resource-group-overview.md)
- [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md)
- [Présentation d’Azure Resource Manager](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0907_2016-->