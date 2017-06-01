---
title: "Configurer des adresses IP pour des cartes réseau Azure | Documents Microsoft"
description: "Découvrez comment ajouter, modifier et supprimer des adresses IP publiques et privées pour des cartes réseau."
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
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: cf7dbc648136897772bbd068687313eec16bed75
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017


---

# <a name="add-change-or-remove-ip-addresses-for-azure-network-interfaces"></a>Ajouter, modifier ou supprimer des adresses IP pour des cartes réseau Azure

Découvrez comment ajouter, modifier et supprimer des adresses IP publiques et privées pour des cartes réseau (NIC). Les adresses IP privées affectées à une carte réseau permettent à une machine virtuelle de communiquer avec Internet ainsi qu’avec d’autres ressources connectées à un réseau virtuel Azure. Les adresses IP publiques affectées à une carte réseau permettent une communication entrante avec une machine virtuelle à partir d’Internet. 

Si vous devez créer, modifier ou supprimer des cartes réseau, voir [Paramètres et tâches de carte virtuelle](virtual-network-network-interface.md). Si vous devez ajouter ou supprimer des cartes réseau de machines virtuelles, voir [Ajouter ou supprimer des cartes réseau](virtual-network-network-interface-vm.md). 


## <a name="before"></a>Avant de commencer

Avant de suivre les étapes décrites dans toute section de cet article, accomplissez les tâches suivantes :

- Pour découvrir les limites des adresses IP publiques et privées, voir [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Connectez-vous au portail Azure, à Azure CLI ou à Azure PowerShell avec un compte Azure. Si vous n’avez pas encore de compte Azure, [créez votre compte Azure gratuit](https://azure.microsoft.com/free).
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, installez et configurez Azure PowerShell en suivant les étapes décrites dans l’article [Comment installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell installées sont celles de la version la plus récente. Pour obtenir de l’aide sur les commandes PowerShell ainsi que des exemples, entrez `get-help <command> -full`.
- Si vous utilisez des commandes d’Azure CLI pour accomplir des tâches décrites dans cet article, installez et configurez Azure CLI en procédant de la manière décrite dans l’article [Comment installer et configurer Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`.

## <a name="about"></a>À propos des cartes réseau et des adresses IP

Plusieurs adresses IP privées et publiques peuvent être affectées à chaque carte réseau, dans le respect des [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). L’affectation d’adresses IP multiples à une carte réseau est notamment utile dans les scénarios suivants :
- D’héberger plusieurs sites web ou services avec des adresses IP différentes et des certificats SSL sur un serveur unique.
- Une machine virtuelle joue le rôle d’appliance virtuelle réseau, comme un pare-feu ou un équilibreur de charge.
- La possibilité d’ajouter l’une des adresses IP privées pour toutes les cartes réseau à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de la carte réseau principale pouvait être ajoutée à un pool principal. Pour plus d’informations sur la façon d’équilibrer la charge entre plusieurs configurations IP, consultez l’article [Équilibrage de la charge entre plusieurs configurations IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Les adresses IP sont attribuées à une configuration IP. Une configuration IP **principale** est toujours affectée à une carte réseau, mais plusieurs configurations **secondaires** peuvent également lui être affectées. Au moins l’un des deux types d’adresses suivants est affecté à chaque configuration IP :
- **Privée :** les adresses IP privées permettent à une machine virtuelle de communiquer avec d’autres ressources connectées au réseau virtuel dans lequel figure la carte réseau. Au moins une adresse IP privée doit être affectée à une configuration IP. Les serveurs DHCP Azure affectent l’adresse IP privée de la configuration IP principale de la carte réseau à la carte réseau située dans le système d’exploitation de la machine virtuelle. Une adresse IP privée permet également à la machine virtuelle de communiquer en sortie vers Internet. Les connexions sortantes font l’objet de traductions d’adresses réseau sources (SNAT). Pour en savoir plus sur la connectivité Internet sortante d’Azure, voir [À propos de la connectivité Internet sortante d’Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Vous ne pouvez communiquer en entrée vers l’adresse IP privée d’une machine virtuelle à partir d’Internet.
- **Publique :** les adresses IP publiques permettent une connectivité entrante vers une machine virtuelle à partir d’Internet. Les connexions sortantes à Internet ne font pas l’objet de traductions d’adresses réseau sources (SNAT). Vous pouvez affecter une adresse IP publique à une configuration IP, mais ce n’est pas obligatoire. Pour en savoir plus sur les adresses IP publiques, voir [Adresses IP publiques](virtual-network-public-ip-address.md).

Le nombre d’adresses IP publiques et privées que vous pouvez affecter à une carte réseau est limité. Pour en savoir plus, consultez l’article [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Les adresses IP privées et publiques peuvent être affectées à l’aide des méthodes d’allocation suivantes :
- **Dynamique :** les adresses IP publiques et privées dynamique sont affectées par défaut. Les adresses dynamiques peuvent changer si la machine virtuelle est arrêtée (désallouée), puis redémarrée. Si vous ne souhaitez pas que l’adresse IP change pendant la durée de vie de la machine virtuelle, utilisez une adresse statique.
- **Statique :** une adresse statique ne change pas tant que la machine virtuelle n’est pas supprimé. Vous attribuez une adresse IP privée statique à partir de l’espace d’adressage pour le sous-réseau auquel la carte réseau est attachée . Pour en savoir plus sur la façon dont Azure affecte les adresses IP publiques statiques, voir [Adresse IP publique](virtual-network-public-ip-address.md).

## <a name="create-ip-config"></a>Ajouter des adresses IP

Vous pouvez ajouter autant d’adresses IP que nécessaire à une carte réseau, dans le respect des [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur la carte réseau pour laquelle vous souhaitez ajouter une adresse IP.
4. Dans la section **PARAMÈTRES** du panneau correspondant à la carte réseau sélectionnée, cliquez sur **Configurations IP**.
5. Cliquez sur **+ Ajouter** dans le panneau qui s’ouvre pour les configurations IP.
6. Indiquez les éléments suivants, puis cliquez sur **OK** pour fermer le panneau **Ajouter une configuration IP** :

    |Paramètre|Requis ?|Détails|
    |---|---|---|
    |Nom|Oui|Doit être unique pour la carte réseau|
    |Type|Oui|Étant donné que vous ajoutez une configuration IP à une carte réseau existante et que chaque carte réseau doit disposer d’une configuration IP principale, la seule option possible est **Secondaire**.|
    |Méthode d’affectation d’adresses IP privées|Oui|Les adresses **dynamiques** peuvent être modifiées si la machine virtuelle est redémarrée sans passer par l’état Arrêté (Désalloué). Azure affecte une adresse disponible à partir de l’espace d’adressage du sous-réseau auquel la carte réseau est connectée. Les adresses **statiques** ne sont pas libérées si la carte réseau n’est pas supprimée. Spécifiez une adresse IP à partir de la plage d’espace d’adressage du sous-réseau, qui n’est pas actuellement utilisée par une autre configuration IP.|
    |Adresse IP publique|Non|**Désactivée :** aucune ressource d’adresse IP publique n’est actuellement associée à la configuration IP. **Activée :** sélectionnez une adresse IP publique existante ou créez-en une. Pour savoir comment créer une adresse IP publique, consultez l’article [Adresses IP publiques](virtual-network-public-ip-address.md#create).|
7. Ajoutez manuellement des adresses IP privées secondaires au système d’exploitation de la machine virtuelle en suivant les instructions de l’article [Affecter plusieurs adresses IP aux machines virtuelles](virtual-network-multiple-ip-addresses-portal.md#os-config). N’ajoutez pas d’adresse IP publique au système d’exploitation de la machine virtuelle.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-config"></a>Modifier les paramètres d’adresse IP

Vous pouvez modifier la méthode d’affectation d’une adresse IP, modifier l’adresse IP statique, ou modifier l’adresse IP publique affectées à une carte réseau. Si vous modifiez l’adresse IP privée d’une configuration IP secondaire associée à une carte réseau secondaire dans une machine virtuelle (pour en savoir plus, voir [Cartes réseau principales et secondaires](virtual-network-network-interface-vm.md#about)), mettez la machine virtuelle dans l’état arrêté (désalloué) avant d’effectuer les opérations suivantes : 

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur la carte réseau dont souhaitez afficher ou modifier les paramètres d’adresse IP.
4. Dans la section **PARAMÈTRES** du panneau correspondant à la carte réseau sélectionnée, cliquez sur **Configurations IP**.
5. Cliquez sur la configuration IP que vous souhaitez modifier dans la liste du panneau qui s’affiche pour les configurations IP.
6. Modifiez les paramètres comme vous le souhaitez en utilisant les informations relatives aux paramètres fournies à l’étape 6 de la section [Ajouter une configuration IP](#create-ip-config) de cet article. Pour fermer le panneau de la configuration IP que vous avez modifiée, cliquez sur **Enregistrer**.

>[!NOTE]
>Si la carte réseau principale dispose de plusieurs configurations IP et si vous modifiez l’adresse IP privée de la configuration IP principale, vous devez réaffecter manuellement toutes les adresses IP secondaires à la carte réseau dans Windows (non requis pour Linux). Pour affecter manuellement des adresses IP à une carte réseau au sein d’un système d’exploitation, consultez l’article [Affecter plusieurs adresses IP aux machines virtuelles](virtual-network-multiple-ip-addresses-portal.md#os-config). N’ajoutez pas d’adresse IP publique au système d’exploitation de la machine virtuelle.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-ip-config"></a>Supprimer des adresses IP

Vous pouvez supprimer des adresses IP privées et publiques à partir d’une carte réseau, mais une carte réseau doit toujours avoir au moins une adresse IP privée.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur la carte réseau dont vous souhaitez supprimer des adresses IP.
4. Dans la section **PARAMÈTRES** du panneau correspondant à la carte réseau sélectionnée, cliquez sur **Configurations IP**.
5. Cliquez avec le bouton droit sur une configuration IP secondaire (vous ne pouvez pas supprimer la configuration principale) que vous souhaitez supprimer. Cliquez ensuite sur **Supprimer**, puis sur **Oui** pour confirmer la suppression. Si une ressource d’adresse IP publique est associée à la configuration, la ressource est dissociée de la configuration IP, mais la ressource n’est pas supprimée.
6. Fermez le panneau **Configurations IP**.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Étapes suivantes
Pour créer une machine virtuelle avec plusieurs cartes réseau ou adresses IP, lisez les articles suivants :

**Commandes**

|Tâche|Outil|
|---|---|
|Créer une machine virtuelle avec plusieurs cartes d’interface réseau|[INTERFACE DE LIGNE DE COMMANDE](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Créer une machine virtuelle à carte réseau unique avec plusieurs adresses IP|[INTERFACE DE LIGNE DE COMMANDE](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

