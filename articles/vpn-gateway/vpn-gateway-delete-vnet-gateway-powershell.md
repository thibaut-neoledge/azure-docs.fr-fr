---
title: "Guide pratique de suppression d’une passerelle de réseau virtuel : PowerShell : Azure Resource Manager | Microsoft Docs"
description: "Supprimez une passerelle de réseau virtuel avec PowerShell dans le modèle de déploiement Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 523166a2dc509f13f62aef0e83799849a04d7efb
ms.lasthandoff: 03/14/2017


---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Supprimer une passerelle de réseau virtuel avec PowerShell

Deux approches sont possibles afin de supprimer une passerelle de réseau virtuel pour une configuration de passerelle VPN.

- Si vous souhaitez tout supprimer et recommencer, comme dans le cas d’un environnement de test, vous pouvez supprimer tout un groupe de ressources. Supprimer un groupe de ressources supprime toutes les ressources du groupe. Cette approche n’est recommandée que si vous ne souhaitez conserver aucune des ressources du groupe de ressources. Vous ne pouvez pas choisir de supprimer uniquement certaines ressources avec cette approche.

- Si vous souhaitez conserver certaines ressources de votre groupe de ressources, la suppression d’une passerelle de réseau virtuel devient légèrement plus complexe. Avant de supprimer la passerelle de réseau virtuel, vous devez commencer par supprimer toutes les ressources qui dépendent de la passerelle. Les étapes à suivre dépendent du type de connexions que vous avez créées et des ressources dépendantes de chaque connexion.

##<a name="before-beginning"></a>Avant tout chose

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Téléchargez la dernière version des applets de commande PowerShell Azure Resource Manager.
Téléchargez et installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur le téléchargement et l’installation des applets de commande PowerShell, consultez la page [Guide pratique d’installation et de configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="2-connect-to-your-azure-account"></a>2. Connectez-vous à votre compte Azure. 
Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

    Login-AzureRmAccount

Vérifiez les abonnements associés au compte.

    Get-AzureRmSubscription

Si vous avez plusieurs abonnements, spécifiez celui que vous souhaitez utiliser.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"



##<a name="S2S"></a>Supprimer une passerelle VPN de site à site

Pour supprimer une passerelle de réseau virtuel pour une configuration S2S, vous devez d’abord supprimer chaque ressource liée à la passerelle de réseau virtuel. Les ressources doivent être supprimées dans un certain ordre en raison des dépendances. Lorsque vous travaillez avec les exemples ci-dessous, certaines valeurs doivent être spécifiquement appelées, tandis que d’autres sont des résultats de sortie. Nous utilisons les valeurs suivantes dans les exemples à des fins de démonstration :

Nom du réseau virtuel : VNet1<br>
Nom du groupe de ressources : RG1<br>
Nom de la passerelle de réseau virtuel : GW1<br>


###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Récupérez la passerelle de réseau virtuel que vous souhaitez supprimer.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Vérifiez si la passerelle de réseau virtuel possède des connexions.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
    $Conns=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}

###<a name="3-delete-all-connections"></a>3. Supprimez toutes les connexions.
Vous serez peut-être invité à confirmer la suppression de chacune des connexions.

    $Conns | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}

###<a name="4-get-the-list-of-the-corresponding-local-network-gateways"></a>4. Récupérez la liste des passerelles de réseau local correspondantes.

    $LNG=Get-AzureRmLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
    
###<a name="5-delete-the-local-network-gateways"></a>5. Supprimez les passerelles de réseau local.
Vous serez peut-être invité à confirmer la suppression de chacune des passerelles de réseau local.

    $LNG | ForEach-Object {Remove-AzureRmLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}

###<a name="6-delete-the-virtual-network-gateway"></a>6. Supprimez la passerelle de réseau virtuel.
Vous serez peut-être invité à confirmer la suppression de la passerelle.

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="7-get-the-ip-configurations-of-the-virtual-network-gateway"></a>7. Récupérez les configurations IP de la passerelle de réseau virtuel.

    $GWIpConfigs = $Gateway.IpConfigurations

###<a name="8-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>8. Récupérez la liste des adresses IP publiques utilisées pour cette passerelle de réseau virtuel. 
Si la passerelle de réseau virtuel était active-active, vous verrez deux adresses IP publiques.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="9-delete-the-public-ips"></a>9. Supprimez les adresses IP publiques.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}

###<a name="10-delete-the-gateway-subnet"></a>10. Supprimez le sous-réseau de la passerelle.

    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub

##<a name="v2v"></a>Supprimer une passerelle VPN de réseau virtuel à réseau virtuel

Si vous souhaitez supprimer une passerelle de réseau virtuel pour une configuration V2V, vous devez d’abord supprimer chaque ressource liée à la passerelle de réseau virtuel. Les ressources doivent être supprimées dans un certain ordre en raison des dépendances. Lorsque vous travaillez avec les exemples ci-dessous, certaines valeurs doivent être spécifiquement appelées, tandis que d’autres sont des résultats de sortie. Nous utilisons les valeurs suivantes dans les exemples à des fins de démonstration :

Nom du réseau virtuel : VNet1<br>
Nom du groupe de ressources : RG1<br>
Nom de la passerelle de réseau virtuel : GW1<br>


###<a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Récupérez la passerelle de réseau virtuel que vous souhaitez supprimer.

    $Gateway=get-azurermvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. Vérifiez si la passerelle de réseau virtuel possède des connexions.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
Il peut y avoir d’autres connexions à la passerelle de réseau virtuel qui font partie d’un autre groupe de ressources. Recherchez les connexions supplémentaires dans chacun des autres groupes de ressources. Dans cet exemple, nous vérifions les connexions à partir de RG2. Exécutez cette commande pour chacun de vos groupes de ressources qui sont susceptibles d’avoir une connexion à la passerelle de réseau virtuel.

    get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="3-get-the-list-of-connections-in-both-directions"></a>3. Récupérez la liste des connexions dans les deux sens.
Dans la mesure où il s’agit d’une configuration de réseau virtuel à réseau virtuel, vous avez besoin de la liste des connexions dans les deux sens.

    $ConnsL=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
 
Dans cet exemple, nous vérifions les connexions à partir de RG2. Exécutez cette commande pour chacun de vos groupes de ressources qui sont susceptibles d’avoir une connexion à la passerelle de réseau virtuel.
 
    $ConnsR=get-azurermvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}

###<a name="4-delete-all-connections"></a>4. Supprimez toutes les connexions.
Vous serez peut-être invité à confirmer la suppression de chacune des connexions.

    $ConnsL | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
    $ConnsR | ForEach-Object {Remove-AzureRmVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}


###<a name="5-delete-the-virtual-network-gateway"></a>5. Supprimez la passerelle de réseau virtuel.
Vous serez peut-être invité à confirmer la suppression de la passerelle de réseau virtuel.

    Remove-AzureRmVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"

###<a name="6-get-the-ip-configurations-of-the-virtual-network-gateway"></a>6. Récupérez les configurations IP de la passerelle de réseau virtuel.

    $GWIpConfigs = $GW.IpConfigurations

###<a name="7-get-the-list-of-public-ip-addresses-used-for-this-virtual-network-gateway"></a>7. Récupérez la liste des adresses IP publiques utilisées pour cette passerelle de réseau virtuel. 
Si la passerelle de réseau virtuel était active-active, vous verrez deux adresses IP publiques.

    $PubIP=Get-AzureRmPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}

###<a name="8-delete-the-public-ips"></a>8. Supprimez les adresses IP publiques.
Vous serez peut-être invité à confirmer la suppression de l’adresse IP publique.

    $PubIP | foreach-object {remove-azurermpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}

###<a name="9-delete-the-gateway-subnet"></a>9. Supprimez le sous-réseau de la passerelle.
 
    $GWSub = Get-AzureRmVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
    Set-AzureRmVirtualNetwork -VirtualNetwork $GWSub


##<a name="delete"></a>Supprimer une passerelle VPN en supprimant le groupe de ressources

Si vous n’avez pas besoin de conserver de ressources et que vous souhaitez simplement recommencer à zéro, vous pouvez supprimer tout un groupe de ressources. Il s’agit d’un moyen rapide de tout supprimer. Lorsque vous supprimez un groupe de ressources en entier, vous ne pouvez pas sélectionner les ressources à supprimer. Assurez-vous que c’est bien ce que vous souhaitez faire avant d’exécuter l’exemple.


### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Récupérez la liste des groupes de ressources de votre abonnement.

    Get-AzureRmResourceGroup
### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Recherchez le groupe de ressources que vous souhaitez supprimer.
Localisez le groupe de ressources que vous souhaitez supprimer et affichez la liste des ressources qu’il contient. Dans l’exemple, le nom du groupe de ressources est RG1. Modifiez l’exemple pour récupérer la liste de toutes les ressources.

    Find-AzureRmResource -ResourceGroupNameContains RG1

### <a name="3-verify-the-resources-in-the-list"></a>3. Vérifiez les ressources de la liste.
Lorsque la liste est renvoyée, passez-la en revue afin de vérifier que vous souhaitez supprimer toutes les ressources du groupe de ressources, ainsi que le groupe de ressources lui-même. 


### <a name="4-delete-the-resource-group-and-resources"></a>4. Supprimez le groupe de ressources et les ressources.
Pour supprimer le groupe de ressources et toutes les ressources qu’il contient, modifiez l’exemple et exécutez-le.

    Remove-AzureRmResourceGroup -Name RG1

### <a name="5-check-the-status"></a>5. Vérifiez l’état.
Azure met quelque temps à supprimer toutes les ressources. Vous pouvez vérifier l’état de votre groupe de ressources avec cette applet de commande.

    Get-AzureRmResourceGroup -ResourceGroupName RG1

Le résultat retourné indique « Réussi ».

    ResourceGroupName : RG1
    Location          : eastus
    ProvisioningState : Succeeded


