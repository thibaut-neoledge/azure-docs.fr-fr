---
title: "Créer, modifier ou supprimer des réseaux virtuels Azure | Documents Microsoft"
description: "Découvrez comment créer, modifier ou supprimer des réseaux virtuels."
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b577891afc52013b712634dd51e7e28efcfc4482
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-networks"></a>Créer, modifier ou supprimer des réseaux virtuels

Découvrez comment créer et supprimer des réseaux virtuels (VNet), ainsi que modifier des paramètres tels que les serveurs DNS et les espaces d’adressage IP pour des réseaux virtuels existants. Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Il s’agit d’un isolement logique du cloud Azure dédié à votre abonnement. Pour chaque réseau virtuel, vous pouvez :
- Choisir l’espace d’adressage à assigner. Un espace d’adressage se compose d’une ou plusieurs plages d’adresses définies à l’aide d’une notation CIDR telle que 10.0.0.0/16.
- Choisir d’utiliser le serveur DNS fourni par Azure ou votre propre serveur DNS pour chaque réseau virtuel. Ce serveur DNS est assigné à toutes les ressources connectées au réseau virtuel pour résoudre les noms au sein de celui-ci.
- Segmenter le réseau virtuel en sous-réseaux, chacun avec sa propre plage d’adresses, à l’intérieur de l’espace d’adressage du réseau virtuel. Pour savoir comment créer, modifier et supprimer des sous-réseaux, voir [Ajouter, modifier ou supprimer des sous-réseaux](virtual-network-manage-subnet.md).

Cet article explique comment créer, modifier et supprimer des réseaux virtuels créés à l’aide du modèle de déploiement Azure Resource Manager.
 
## <a name="before"></a>Avant de commencer

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous débutez dans l’utilisation de réseaux virtuels, avant de lire cet article, nous vous recommandons de réaliser l’exercice décrit dans [Créer votre premier réseau virtuel Azure](virtual-network-get-started-vnet-subnet.md). Cet exercice vous aidera à vous familiariser avec les réseaux virtuels.
- Pour connaître les limites des réseaux virtuels, voir [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Connectez-vous au portail Azure, à Azure CLI ou à Azure PowerShell avec un compte Azure. Si vous n’avez pas encore de compte Azure, [créez votre compte Azure gratuit](https://azure.microsoft.com/free).
- Si vous utilisez des commandes d’Azure PowerShell pour accomplir les tâches décrites dans cet article, vous devez commencer par [installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell de la version la plus récente sont installées. Pour obtenir de l’aide sur les commandes PowerShell ainsi que des exemples, entrez `get-help <command> -full`.
- Si vous utilisez des commandes d’Azure CLI pour accomplir les tâches décrites dans cet article, vous devez commencer par [installer et configurer Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`.


## <a name="create-vnet"></a>Créer un réseau virtuel

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans le portail Azure, cliquez sur **+ Nouveau**. Dans le panneau **Nouveau** qui s’affiche, cliquez sur **Mise en réseau**. Dans le panneau **Mise en réseau** qui s’affiche, cliquez sur **Réseau virtuel.**
3. Dans le panneau **Réseau virtuel** qui s’affiche, laissez l’option *Resource Manager* activée dans la boîte de dialogue **Sélectionnez un modèle de déploiement**, puis cliquez sur **Créer**.
4. Dans le panneau **Créer un réseau virtuel** qui s’affiche, entrez ou sélectionnez les valeurs des paramètres suivants, puis cliquez sur **Créer**:
    - **Nom** : le nom doit être unique dans le [groupe de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) que vous sélectionnez pour y créer le réseau virtuel. Une fois que le réseau virtuel créé, il n’est plus possible d’en modifier le nom. Vous pouvez créer plusieurs réseaux virtuels au fil du temps. Pour obtenir des suggestions de noms facilitant la gestion de plusieurs réseaux virtuels, voir [Conventions de dénomination](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions).
    - **Espace d’adressage** : spécifiez la notation CIDR. L’espace d’adressage que vous définissez peut être public ou privé (RFC 1918). Que vous définissiez un espace d’adressage public ou privé, celui-ci est accessible uniquement à partir du réseau virtuel, de réseaux virtuels interconnectés ou de réseaux locaux que vous avez connectés au réseau virtuel. Vous ne pouvez pas ajouter les espaces d’adressage suivants :
        - 224.0.0.0/4 (multidiffusion)
        - 255.255.255.255/32 (diffusion)
        - 127.0.0.0/8 (bouclage)
        - 169.254.0.0/16 (lien-local)
        - 168.63.129.16/32 (DNS interne)
    
      Si vous ne pouvez définir qu’un seul espace d’adressage au moment de la création du réseau virtuel, vous pouvez ajouter des espaces d’adressage par la suite. Pour savoir comment procéder, procédez de la manière décrite dans la section [Ajouter ou supprimer des espaces d’adressage](#add-address-spaces) de cet article.

      >[!WARNING]
      >Si des réseaux virtuels comprennent des espaces d’adressage qui se chevauchent avec d’autres réseaux virtuels ou locaux, il n’est pas possible de les interconnecter. Avant de définir un espace d’adressage, songez aux autres réseaux virtuels ou locaux auxquels vous pourriez vouloir connecter le réseau virtuel à l’avenir.
      >
      >
    
    - **Nom du sous-réseau :** le nom doit être unique au sein du réseau virtuel. Une fois que le sous-réseau créé, il n’est plus possible d’en modifier le nom. Lors de la création d’un réseau virtuel, le portail exige que vous définissiez un sous-réseau, même si un réseau virtuel n’est pas indispensable pour avoir des sous-réseaux. Si le portail ne vous permet de définir qu’un seul sous-réseau lors de la création du réseau virtuel, vous pouvez toujours ajouter des sous-réseaux à celui-ci par la suite. Pour savoir comment ajouter un sous-réseau à un réseau virtuel, lisez la section [Créer un sous-réseau](virtual-network-manage-subnet.md#create-subnet) de l’article [Créer, modifier ou supprimer des sous-réseaux](virtual-network-manage-subnet.md). Vous pouvez créer un réseau virtuel comprenant plusieurs sous-réseaux à l’aide de l’interface de ligne de commande ou de PowerShell.

      >[!TIP]
      >Pour filtrer ou contrôler le routage du trafic entre ceux-ci, d’autres sous-réseaux sont souvent créés. Avant de définir des sous-réseaux, songez à la manière dont vous pouvez filtrer et router le trafic entre ceux-ci. Pour en savoir plus sur le filtrage du trafic entre sous-réseaux, voir [Filtrer le trafic réseau avec les groupes de sécurité réseau](virtual-networks-nsg.md). Azure effectue automatiquement le routage entre sous-réseaux, mais vous pouvez modifier les Itinéraires par défaut d’Azure. Pour en savoir plus, voir l’article [Itinéraires définis par l’utilisateur et transfert IP](virtual-networks-udr-overview.md).
      >

    - **Plage d’adresses de sous-réseau :** celles-ci doivent figurer à l’intérieur de l’**Espace d’adressage** vous avez entré pour le réseau virtuel. La plus petite plage que vous puissiez spécifier est /29. Celle-ci fournit huit adresses IP pour le sous-réseau. Azure réserve la première et la dernière adresses dans chaque sous-réseau pour la conformité du protocole. Trois adresses supplémentaires sont réservées à l’usage du service Azure. Par conséquent, un réseau virtuel avec une plage d’adresses de sous-réseau /29 ne compte que trois adresses IP utilisables. Si vous envisagez de connecter un réseau virtuel à une passerelle VPN, vous devez créer un sous-réseau de passerelle. Pour en savoir plus, voir [Sous-réseau de passerelle](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Vous pouvez modifier la plage d’adresses après la création du sous-réseau sous certaines conditions. Pour découvrir comment modifier une plage d’adresses de sous-réseau, lisez la section [Modifier un sous-réseau](#change-subnet) de l’article [Ajouter, modifier ou supprimer des sous-réseaux](virtual-network-manage-subnet.md).
    - **Abonnement :** sélectionnez un [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Un réseau virtuel ne peut pas couvrir plusieurs abonnements, même s’il peut être connecté à des réseaux virtuels dans d’autres abonnements à l’aide d’une passerelle VPN Azure ou d’un appairage de réseau virtuel (VNET Peering). Les ressources Azure que vous connectez au réseau virtuel doivent exister dans le même abonnement.
    - **Groupe de ressources :** sélectionnez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) existant ou créez-en un. Les ressources Azure que vous vous connectez au réseau virtuel peuvent exister dans des groupes de ressources identique ou différents.
    - **Emplacement :** sélectionnez un [emplacement](https://azure.microsoft.com/regions/) Azure (également appelé région). Un réseau virtuel ne peut pas s’étendre sur plusieurs emplacements, même s’il peut être connecté à un réseau virtuel dans un autre emplacement à l’aide d’une passerelle VPN Azure. Les ressources Azure que vous connectez au réseau virtuel doivent exister dans le même emplacement.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network vnet create](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name = "view-vnet"></a>Afficher des réseaux virtuels et des paramètres

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *réseaux virtuels*. Lorsque la mention **réseaux virtuels** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Réseaux virtuels** qui s’affiche, cliquez sur le réseau virtuel dont vous souhaitez afficher les paramètres.
4. Les paramètres suivants sont répertoriés dans le panneau qui s’affiche pour le réseau que vous avez sélectionné :
    - **Vue d’ensemble :** fournit des informations sur le réseau virtuel, telles que son espace d’adressage et ses serveurs DNS. L’illustration suivante montre les paramètres de vue d’ensemble d’un réseau virtuel nommé **MyVNet** :
    
        ![Vue d’ensemble de l’interface réseau](./media/virtual-network-manage-network/vnet-overview.png)

      À partir de ce panneau, vous pouvez déplacer un réseau virtuel vers un autre groupe d’abonnements ou de ressources. Pour savoir comment déplacer un réseau virtuel, voir [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Cet article répertorie les conditions préalables et explique comment déplacer des ressources à l’aide du portail Azure, de PowerShell ou d’Azure CLI. Toutes les ressources connectées au réseau virtuel doivent être déplacées avec celui-ci. 
    - **Espace d’adressage :** les espaces d’adressage assignés au réseau virtuel sont répertoriés. Pour savoir comment ajouter et supprimer des espaces d’adressage, procédez de la manière décrite dans la section [Ajouter ou supprimer des espaces d’adressage](#address-spaces) de cet article.
    - **Appareils connectés :** toutes les ressources connectées au réseau virtuel sont affichées. Dans l’exemple présenté dans l’illustration précédente, trois cartes réseau et un équilibreur de charge sont connectés au réseau virtuel. Toute nouvelle ressource que vous créez et connectez au réseau virtuel est ajoutée à la liste. Si vous supprimez une ressource connectée au réseau virtuel, celle-ci n’apparaît plus dans la liste.
    - **Sous-réseaux :** liste des sous-réseaux du réseau virtuel. Pour savoir comment ajouter et supprimer des sous-réseaux, lisez les sections [Ajouter un sous-réseau](virtual-network-manage-subnet.md#create-subnet) et [Supprimer un sous-réseau](virtual-network-manage-subnet.md#delete-subnet) de l’article [Ajouter, modifier ou supprimer des sous-réseaux](virtual-network-manage-subnet.md).
    - **Serveurs DNS :** vous pouvez spécifier le serveur chargé d’assurer la résolution de noms pour les appareils connectés au réseau virtuel : le serveur DNS interne d’Azure ou le serveur DNS personnalisé. Lorsque vous créez un réseau virtuel via le portail Azure, par défaut, les serveurs DNS d’Azure sont utilisés pour la résolution de noms à l’intérieur d’un réseau virtuel. Pour modifier les serveurs DNS, procédez de la manière décrite dans la section [Ajouter, modifier ou supprimer des serveurs DNS](#dns-servers) de cet article. 
    - **Homologations :** s’il existe des homologations dans l’abonnement, elles sont répertoriées ici. Vous pouvez afficher les paramètres des homologations existantes, ou créer, modifier ou supprimer des homologations. Pour en savoir plus sur les homologations, voir [Homologation de réseaux virtuels](virtual-network-peering-overview.md).
    - **Propriétés :** affiche les paramètres relatifs au réseau virtuel, dont son ID de ressource et l’abonnement au sein duquel il s’inscrit.
    - **Diagramme :** le diagramme fournit une représentation visuelle de tous les appareils connectés au réseau virtuel, ainsi que des informations clés sur ceux-ci. Vous pouvez cliquer sur tout appareil pour le gérer directement via cet affichage.
    - **Paramètres courants d’Azure :** pour en savoir plus sur les paramètres courants d’Azure, voir les articles [Journal d’activité](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Contrôle d’accès (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Balises](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Verrous](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Script d’automatisation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Commandes**

|**Outil**|**Commande**|
|---|---|
|Interface de ligne de commande|[az network vnet show](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/v3.8.0/get-azurermvirtualnetwork/?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="address-spaces"></a>Ajouter ou supprimer des espaces d’adressage

Vous pouvez ajouter ou supprimer des espaces d’adressage pour un réseau virtuel. Les espaces d’adressage doivent être spécifiés dans une notation CIDR et ne peuvent pas se chevaucher au sein d’un même réseau virtuel. Les espaces d’adressage que vous définissez peuvent être publics ou privés (RFC 1918). Que vous définissiez un espace d’adressage public ou privé, l’espace d’adressage au sein d’un réseau virtuel est accessible uniquement à partir de celui-ci, de réseaux virtuels interconnectés ou de réseaux locaux que vous avez connectés au réseau virtuel. Vous ne pouvez pas ajouter les espaces d’adressage suivants :
    - 224.0.0.0/4 (multidiffusion)
    - 255.255.255.255/32 (diffusion)
    - 127.0.0.0/8 (bouclage)
    - 169.254.0.0/16 (lien-local)
    - 168.63.129.16/32 (DNS interne)

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *réseaux virtuels*. Lorsque la mention **réseaux virtuels** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Réseaux virtuels** qui s’affiche, cliquez sur le réseau virtuel pour lequel vous souhaitez ajouter ou supprimer un espace d’adressage.
4. Dans le panneau qui s’affiche pour le réseau virtuel que vous avez sélectionné, dans la section **Paramètres**, cliquez sur **Espace d’adressage**.
5. Dans le panneau qui s’affiche avec les espaces d’adressage, sélectionnez l’une des options suivantes :
    - **Ajouter un espace d’adressage :** entrez le nouvel espace d’adressage. L’espace d’adressage ne peut pas chevaucher un espace d’adressage existant défini pour le réseau virtuel.
    - **Supprimer un espace d’adressage :** cliquez avec le bouton droit sur un espace d’adressage, puis cliquez sur **Supprimer**. S’il existe un sous-réseau dans l’espace d’adressage, vous ne pouvez pas supprimer l’espace d’adressage. Avant de supprimer un espace d’adressage, vous devez supprimer tous les sous-réseaux (et toutes les ressources qui y sont connectées) existant dans celui-ci.
6. Cliquez sur **Save**.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|Resource Manager uniquement|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="dns-servers"></a>Ajouter, modifier ou supprimer des serveurs DNS

Tous les machines virtuelles connectées au réseau virtuel s’inscrivent auprès des serveurs DNS spécifiés pour le réseau virtuel et utilisent le serveur DNS pour la résolution de noms. Chaque carte réseau (NIC) sur un ordinateur virtuel peut avoir ses propres paramètres de serveur DNS. Si une carte réseau a ses propres paramètres de serveur DNS, ceux-ci remplacent les paramètres du serveur DNS pour le réseau virtuel. Pour en savoir plus sur les paramètres DNS de carte réseau, voir [Modifier les paramètres DNS d’une carte réseau](virtual-network-network-interface.md#dns). Pour en savoir plus sur la résolution de noms pour les machines virtuelles et les instances de rôle des services cloud, voir [Résolution de noms pour les machines virtuelles et les instances de rôle](virtual-networks-name-resolution-for-vms-and-role-instances.md).

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *réseaux virtuels*. Lorsque la mention **réseaux virtuels** apparaît dans les résultats de recherche, cliquez dessus. 
3. Dans le panneau **Réseaux virtuels** qui s’affiche, cliquez sur le réseau virtuel dont vous souhaitez modifier les paramètres DNS.
4. Dans le panneau qui s’affiche pour le réseau virtuel que vous avez sélectionné, dans la section **Paramètres**, cliquez sur **Serveurs DNS**.
5. Dans le panneau qui s’affiche avec les serveurs DNS, sélectionnez l’une des options suivantes :
    - **Par défaut (fournie par Azure) :** l’ensemble des noms de ressources et des adresses IP privées sont enregistrés automatiquement sur les serveurs DNS d’Azure. Vous pouvez résoudre les noms de toutes les ressources connectées au même réseau virtuel. Vous ne pouvez pas utiliser cette option pour résoudre les noms des réseaux virtuels. Pour résoudre les noms des réseaux virtuels, vous devez utiliser un serveur DNS personnalisé.
    - **Personnalisé :** vous pouvez ajouter un ou plusieurs serveurs, jusqu’à la limite qu’Azure autorise pour un réseau virtuel. Pour en savoir plus sur ces limites des serveurs DNS, voir [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Vous disposez des options suivantes :
        - **Ajouter une adresse :** ajoute un serveur DNS à la liste des serveurs DNS de votre réseau virtuel, et inscrit les serveurs DNS auprès d’Azure. Si vous déjà inscrit un serveur DNS auprès d’Azure, vous pouvez le sélectionner dans la liste qui s’affiche. 
        - **Supprimer une adresse :** cliquez sur le **X** en regard du serveur que vous souhaitez supprimer. La suppression du serveur a pour seul effet de retirer celui-ci de la liste de ce réseau virtuel. Le serveur DNS reste inscrit dans Azure à l’usage de vos autres réseaux virtuels. 
        - **Réorganiser les adresses des serveurs DNS** : il est important que vous vous assuriez de répertorier vos serveurs DNS dans l’ordre approprié pour votre environnement. Les listes de serveurs DNS ne fonctionnent pas sur le modèle du tourniquet (round-robin). Elles sont utilisées dans l’ordre où elles sont spécifiées. Si le premier serveur DNS de la liste est accessible, le client utilise celui-ci, qu’il fonctionne correctement ou non. Supprimez tous les serveurs DNS répertoriés, puis rajoutez-les dans l’ordre souhaité.
        - **Modifier une adresse** : mettez en surbrillance le serveur DNS dans la liste, puis tapez le nouveau nom.
6. Cliquez sur **Save**.
7. Redémarrez les machines virtuelles connectées au réseau virtuel afin que les nouveaux paramètres de serveur DNS leur soient appliqués. Les machines virtuelles continuent d’utiliser leurs paramètres DNS actifs jusqu’à ce qu’elles soient redémarrés.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network vnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-vnet"></a>Supprimer un réseau virtuel

Vous ne pouvez supprimer un réseau virtuel que si aucune ressource n’y est connectée. Si des ressources sont connectées à un sous-réseau du réseau virtuel, vous devez commencer par supprimer les ressources connectées à tous les sous-réseaux du réseau virtuel. Les instructions relatives à la suppression d’une ressource varient en fonction de celle-ci. Pour savoir comment supprimer une ressource connectée à des sous-réseaux, lisez la documentation relative à ce type de ressource. Pour supprimer un réseau virtuel, procédez comme suit :

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Contributeur de réseaux pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la boîte de dialogue contenant le texte *Rechercher des ressources* en haut du portail Azure, tapez *réseaux virtuels*. Lorsque la mention **réseaux virtuels** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Réseaux virtuels** qui s’affiche, cliquez sur le réseau virtuel à supprimer.
4. Vérifiez qu’aucun appareil n’est connecté au réseau virtuel en cliquant sur **Appareils connectés** dans la section **Paramètres** du panneau qui s’affiche pour le réseau virtuel que vous avez sélectionné. Si un appareil est connecté, vous devez le supprimer avant de pouvoir supprimer le réseau virtuel.  Si aucun appareil n’est connecté, dans le panneau, cliquez sur **Vue d’ensemble**.
5. Cliquez sur l’icône **supprimer** en haut du panneau. 
6. Cliquez sur **Oui** pour confirmer la suppression du réseau virtuel.


**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[azure network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment créer une machine virtuelle et la connecter à un réseau virtuel, voir [Créer un réseau virtuel et le connecter à des machines virtuelles](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines).
- Pour savoir comment filtrer le trafic réseau entre des sous-réseaux au sein d’un réseau virtuel, voir [Créer des groupes de sécurité réseau à l’aide du portail Azure](virtual-networks-create-nsg-arm-pportal.md).
- Pour savoir comment apparier un réseau virtuel à un autre, voir [Créer une homologation de réseaux virtuels à l’aide du portail Azure](virtual-networks-create-vnetpeering-arm-portal.md#peering-vnets-in-the-same-subscription).
- Pour découvrir les options de connexion d’un réseau virtuel à un réseau local, voir [À propos de la passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namediagramsaconnection-topology-diagrams).

