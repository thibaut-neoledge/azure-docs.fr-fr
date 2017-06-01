---
title: "Créer, modifier ou supprimer des sous-réseaux de réseau virtuel Azure | Documents Microsoft"
description: "Découvrez comment créer, modifier ou supprimer des sous-réseaux de réseau virtuel."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 38dcdf2f313c236a507e6a418c39812da16c6345
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-network-subnets"></a>Créer, modifier ou supprimer des sous-réseaux de réseau virtuel

Découvrez comment créer, modifier ou supprimer des sous-réseaux de réseau virtuel. Si vous n’êtes pas familiarisé avec les réseaux virtuels, avant de créer, modifier ou supprimer des sous-réseaux, nous vous recommandons de lire les articles [Vue d’ensemble des réseaux virtuels](virtual-networks-overview.md) et [Créer, modifier ou supprimer des réseaux virtuels](virtual-network-manage-network.md). Les ressources Azure qui peuvent se connecter à un réseau virtuel sont connectées à un sous-réseau à l’intérieur d’un réseau virtuel. Plusieurs sous-réseaux sont généralement créés au sein d’un réseau virtuel afin de :
- **Filtrer le trafic entre sous-réseaux :** des groupes de sécurité réseau (NSG) peuvent être appliqués à des sous-réseaux pour filtrer les trafics réseau entrant et sortant pour toutes les ressources (telles que des machines virtuelles) connectées au réseau virtuel. Pour en savoir plus sur la création de groupes de sécurité réseau, voir [Créer des groupes de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md).
- **Contrôler le routage entre les sous-réseaux :** Azure crée des itinéraires par défaut de façon à ce que le trafic soit routé automatiquement entre les sous-réseaux. Vous pouvez remplacer les itinéraires Azure par défaut en créant des itinéraires définis par l’utilisateur (UDR). Pour plus d’informations sur les UDR, voir [Itinéraires définis par l’utilisateur](virtual-network-create-udr-arm-ps.md). 

Cet article explique comment créer, modifier et supprimer des sous-réseaux pour des réseaux virtuels créés à l’aide du modèle de déploiement Azure Resource Manager.
 
## <a name="before"></a>Avant de commencer

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous débutez dans l’utilisation de réseaux virtuels et de sous-réseaux dans Azure, avant de lire cet article, nous vous recommandons de réaliser l’exercice décrit dans [Créer votre premier réseau virtuel Azure](virtual-network-get-started-vnet-subnet.md). Cet exercice vous permettra de vous familiariser avec les réseaux virtuelles et les sous-réseaux.
- Pour en savoir plus sur les limites des sous-réseaux et des réseaux virtuels, voir [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Connectez-vous au portail Azure, à Azure CLI ou à Azure PowerShell avec un compte Azure. Si vous n’avez pas encore de compte Azure, [créez votre compte Azure gratuit](https://azure.microsoft.com/free).
- Si vous utilisez des commandes d’Azure PowerShell pour accomplir les tâches décrites dans cet article, vous devez commencer par [installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell de la version la plus récente sont installées. Pour obtenir de l’aide sur les commandes PowerShell ainsi que des exemples, entrez `get-help <command> -full`.
- Si vous utilisez des commandes d’Azure CLI pour accomplir les tâches décrites dans cet article, vous devez commencer par [installer et configurer Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`.


## <a name="create-subnet"></a>Créer un sous-réseau

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *réseaux virtuels*. Lorsque la mention **réseaux virtuels** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Réseaux virtuels** qui s’affiche, cliquez sur le réseau virtuel auquel vous souhaitez ajouter un sous-réseau.
4. Dans le volet qui s’affiche pour le réseau virtuel que vous avez sélectionné, cliquez sur **Sous-réseaux**.
5. Cliquez sur **+ Sous-réseau**.
6. Dans le panneau **Ajouter un sous-réseau**, entrez les valeurs des paramètres suivants :
    - **Nom :** le nom doit être unique au sein du réseau virtuel.
    - **Plage d’adresses :** la plage doit être unique au sein de l’espace d’adressage pour le réseau virtuel et ne peut pas chevaucher d’autres plages d’adresses de sous-réseau au sein du réseau virtuel. L’espace d’adressage doit être spécifié à l’aide de la notation CIDR. Par exemple, dans un réseau virtuel avec l’espace d’adressage 10.0.0.0/16, vous pouvez définir l’espace d’adressage de sous-réseau 10.0.0.0/24. La plus petite plage que vous puissiez spécifier est /29. Celle-ci fournit huit adresses IP pour le sous-réseau. Azure réserve la première et la dernière adresses dans chaque sous-réseau pour la conformité du protocole. Trois adresses supplémentaires sont réservées à l’usage du service Azure. Par conséquent, définir un sous-réseau avec un plage d’adresses /29 produit trois adresses IP utilisables dans le sous-réseau. Si vous envisagez de connecter un réseau virtuel à une passerelle VPN, vous devez créer un sous-réseau de passerelle. Pour en savoir plus, voir [Sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Vous pouvez modifier la plage d’adresses après la création du sous-réseau sous certaines conditions. Pour savoir comment modifier une plage d’adresses de sous-réseau, lisez la section [Modifier un sous-réseau](#change-subnet) de cet article.
    - **Groupe de sécurité réseau (NSG) :** vous pouvez éventuellement associer un groupe de sécurité réseau existant au sous-réseau pour contrôler les filtrage des trafics réseau entrant et sortant pour le sous-réseau. Le groupe de sécurité réseau doit exister dans le même abonnement et le même emplacement que le réseau virtuel, et être créé à l’aide du modèle de déploiement Resource Manager. Pour en savoir plus sur la création de groupes de sécurité réseau, voir [Groupes de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md).
    - **Table de routage :** vous pouvez éventuellement associer une table de routage existante au sous-réseau pour contrôler le routage du trafic réseau vers d’autres réseaux. La table de routage doit exister dans le même abonnement et le même emplacement que le réseau virtuel, et être créée à l’aide du modèle de déploiement Resource Manager. Pour en savoir plus sur la création de tables de routage, voir [Itinéraires définis par l’utilisateur](virtual-network-create-udr-arm-ps.md).
    - **Utilisateurs** : vous pouvez contrôler l’accès au sous-réseau à l’aide de rôles intégrés ou de vos propres rôles personnalisés. Pour en savoir plus sur l’attribution de rôles et d’utilisateurs pour l’accès au sous-réseau, voir [Utiliser l’attribution de rôle pour gérer l’accès à vos ressources Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Cliquez sur le bouton **OK** pour ajouter le sous-réseau au réseau virtuel que vous avez sélectionné.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Modifier les paramètres de sous-réseau

Vous pouvez modifier le groupe de sécurité réseau, les tables de routage et l’accès utilisateur au sous-réseau avec les ressources connectées à un sous-réseau. Pour en savoir plus sur ces paramètres, lisez l’étape 6 de la section [Créer un sous-réseau](#create-subnet)de cet article. Pour que vous puissiez modifier l’espace d’adressage d’un sous-réseau, aucune ressource ne peut être connectée au sous-réseau. Si des ressources sont connectées au sous-réseau, vous devez supprimer celles-ci avant de pouvoir modifier la plage d’adresses. Les instructions relatives à la suppression d’une ressource varient en fonction de celle-ci. Pour savoir comment supprimer une ressource connectée à des sous-réseaux, lisez la documentation relative à ce type de ressource. Pour modifier la plage d’adresses pour un sous-réseau, procédez comme suit :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *réseaux virtuels*. Lorsque la mention **réseaux virtuels** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Réseaux virtuels** qui s’affiche, cliquez sur le réseau virtuel pour lequel vous voulez modifier une plage d’adresses de sous-réseau.
4. Cliquez sur le sous-réseau pour lequel vous voulez modifier la plage d’adresses.
5. Dans le panneau qui s’affiche pour le sous-réseau que vous avez sélectionné, entrez la nouvelle plage d’adresses dans la zone **Plage d’adresses**. La plage doit être unique au sein de l’espace d’adressage pour le réseau virtuel et ne peut pas chevaucher d’autres plages d’adresses de sous-réseau au sein du réseau virtuel. L’espace d’adressage doit être spécifié à l’aide de la notation CIDR. Par exemple, dans un réseau virtuel avec l’espace d’adressage 10.0.0.0/16, vous pouvez définir l’espace d’adressage de sous-réseau 10.0.0.0/24. La plus petite plage que vous puissiez spécifier est /29. Celle-ci fournit huit adresses IP pour le sous-réseau. Azure réserve la première et la dernière adresses dans chaque sous-réseau pour la conformité du protocole. Trois adresses supplémentaires sont réservées à l’usage du service Azure. Par conséquent, un sous-réseau avec une plage d’adresses /29 compte trois adresses IP utilisables. Si vous envisagez de connecter un réseau virtuel à une passerelle VPN, vous devez créer un sous-réseau de passerelle. Pour en savoir plus, voir [Sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Vous pouvez modifier la plage d’adresses après la création du sous-réseau sous certaines conditions. Pour savoir comment modifier une plage d’adresses de sous-réseau, lisez la section [Modifier un sous-réseau](#change-subnet) de cet article.
6. Cliquez sur **Save**.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Supprimer un sous-réseau

Vous pouvez supprimer un sous-réseau uniquement si aucune ressource n’y est connectée. Si des ressources sont connectées au sous-réseau, vous devez supprimer celles-ci. Les instructions relatives à la suppression d’une ressource varient en fonction de celle-ci. Pour savoir comment supprimer une ressource connectée à des sous-réseaux, lisez la documentation relative à ce type de ressource.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *réseaux virtuels*. Lorsque la mention **réseaux virtuels** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Réseaux virtuels** qui s’affiche, cliquez sur le réseau virtuel dont vous souhaitez supprimer un sous-réseau.
4. Dans le panneau qui s’affiche pour le réseau virtuel que vous avez sélectionné, sous **Paramètres**, cliquez sur **Sous-réseaux**.
5. Dans la liste des sous-réseaux qui s’affiche dans le panneau des sous-réseaux, cliquez avec le bouton droit sur le sous-réseau que vous souhaitez supprimer. Cliquez ensuite sur **Supprimer**, puis sur **Oui** pour supprimer le sous-réseau.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment créer une machine virtuelle et la connecter à un sous-réseau, voir [Créer un réseau virtuel et le connecter à des machines virtuelles](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines).
