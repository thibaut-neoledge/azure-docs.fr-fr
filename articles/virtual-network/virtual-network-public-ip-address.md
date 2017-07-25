---
title: "Créer, modifier ou supprimer des adresses IP publiques Azure | Microsoft Docs"
description: "Découvrez comment créer, modifier et supprimer des adresses IP publiques."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 47237fe499cd9244266487cd97f6be0d2cb2e977
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017


---

# <a name="create-change-or-delete-public-ip-addresses"></a>Créer, modifier ou supprimer des adresses IP publiques

Découvrez les adresses IP publiques et apprenez à les créer, les modifier et les supprimer. Une adresse IP publique est une ressource disposant de ses propres paramètres configurables. Affecter une adresse IP publique à d’autres ressources Azure permet :
- une connectivité Internet entrante pour les ressources comme les machines virtuelles Azure (VM), les groupes de machines virtuelles Azure identiques, la passerelle VPN Azure et les équilibreurs Azure Load Balancer accessibles sur Internet ;
- une connectivité sortante à Internet qui ne fait pas l’objet d’une traduction d’adresses réseau (NAT). Par exemple, une machine virtuelle peut communiquer en sortie vers Internet sans adresse IP publique affectée, mais son adresse fait l’objet d’une traduction d’adresses réseau par Azure. Pour en savoir plus sur les connexions sortantes des ressources Azure, lisez l’article [Comprendre les connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Cet article explique comment utiliser des adresses IP publiques déployées via le modèle de déploiement Azure Resource Manager.

## <a name="before"></a>Avant de commencer

Avant d’effectuer une étape de cet article, quelle que soit sa section, effectuez les tâches suivantes :

- Consultez attentivement l’article [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) pour en savoir plus sur les limites concernant les adresses IP publiques.
- Connectez-vous au portail Azure, à l’interface de ligne de commande (CLI) d’Azure ou à Azure PowerShell avec un compte Azure. Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, installez et configurez Azure PowerShell en suivant les étapes décrites dans l’article [Comment installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell installées sont celles de la version la plus récente. Pour obtenir de l’aide sur les commandes PowerShell ainsi que des exemples, entrez `get-help <command> -full`.
- Si vous utilisez des commandes d’Azure CLI pour accomplir des tâches décrites dans cet article, installez et configurez Azure CLI en procédant de la manière décrite dans l’article [Comment installer et configurer Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`.

Le coût des adresses IP publiques est modique. Pour voir les prix, consultez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses). 

## <a name="create"></a>Créer une adresse IP publique

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, entrez *adresses ip publique*. Lorsque **Adresses IP publiques** s’affiche dans les résultats de recherche, cliquez dessus.
3. Cliquez sur **+ Ajouter** dans le panneau **Adresse IP publique** qui s’affiche.
4. Dans le panneau **Créer une adresse IP publique** qui s’affiche, entrez ou sélectionnez les valeurs correspondant aux paramètres suivants, puis cliquez sur **Créer** :

    |Paramètre|Requis ?|Détails|
    |---|---|---|
    |Nom|Oui|Le nom doit être unique au sein du groupe de ressources que vous avez sélectionné.|
    |Affectation d’adresses IP|Oui|**Dynamique :** les adresses dynamiques sont affectées uniquement une fois que l’adresse IP publique est associée à une carte d’interface réseau attachée à une machine virtuelle et que la machine virtuelle est démarrée pour la première fois. Les adresses dynamiques peuvent changer si la machine virtuelle à laquelle la carte d’interface réseau est attachée est arrêtée (libérée). L’adresse reste la même si la machine virtuelle est redémarrée ou arrêtée (mais pas libérée). **Statique :** des adresses statiques sont affectées lors de la création de l’adresse IP publique. Les adresses statiques ne changent pas même si la machine virtuelle est arrêtée (libérée). L’adresse est libérée uniquement lorsque la carte d’interface réseau est supprimée. Vous pouvez modifier la méthode d’affectation après la création de la carte d’interface réseau.|
    |Délai d’inactivité (minutes)|Non|Durée (en minutes) de maintien d’une connexion TCP ou HTTP ouverte sans utiliser les clients pour envoyer des messages keep-alive.|
    |Étiquette du nom DNS|Non|Doit être unique dans l’emplacement Azure où vous créez le nom (pour tous les abonnements et tous les clients). Le service DNS public Azure inscrit automatiquement le nom et l’adresse IP pour que vous puissiez vous connecter à une ressource avec le nom. Azure ajoute *emplacement.cloudapp.azure.com* (où emplacement est l’emplacement que vous sélectionnez) au nom que vous indiquez pour créer le nom DNS complet.|
    |Abonnement|Oui|Doit exister dans le même [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que la ressource à laquelle vous voulez associer l’adresse IP publique.|
    |Groupe de ressources|Oui|Peut exister dans un [groupe de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) identique ou différent de celui de la ressource à laquelle vous voulez associer l’adresse IP publique.|
    |Lieu|Oui|Doit exister au même [emplacement](https://azure.microsoft.com/regions) (également appelé région) que la ressource à laquelle vous voulez associer l’adresse IP publique.|

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="change"></a>Afficher une adresse IP publique, modifier ses paramètres ou la supprimer

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, entrez *adresses ip publique*. Lorsque **Adresses IP publiques** s’affiche dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Adresses IP publiques** qui s’affiche, cliquez sur le nom de l’adresse IP publique que vous voulez afficher, dont vous souhaitez modifier les paramètres ou que vous voulez supprimer.
4. Dans le panneau affiché pour l’adresse IP publique, effectuez l’une des étapes suivantes selon que vous souhaitez supprimer l’adresse IP publique ou la modifier.
    - **Afficher** : la section Vue d’ensemble du panneau affiche les paramètres clés de l’adresse IP publique, tels que l’interface réseau à laquelle elle est associée (si l’adresse est associée à une interface réseau).
    - **Supprimer** : pour supprimer l’adresse IP publique, cliquez sur **Supprimer** dans la section **Vue d’ensemble** du panneau. Si l’adresse est actuellement associée à une configuration IP, elle ne peut pas être supprimée. Si l’adresse est actuellement associée à une configuration, cliquez sur **Dissocier** pour la dissocier de la configuration IP.
    - **Modifier** : cliquez sur **Configuration**. Modifiez des paramètres en utilisant les informations indiquées à l’étape 4 de la section [Créer une adresse IP publique](#create) de cet article. Pour modifier l’affectation et passer des adresses statiques à des adresses dynamiques, vous devez commencer par dissocier l’adresse IP publique de la configuration IP à laquelle elle est associée. Vous pouvez modifier la méthode d’affectation et la définir sur dynamique, puis cliquer sur **Associer** pour associer l’adresse IP à la même configuration IP, à une autre configuration ou vous pouvez la laisser dissociée. Pour dissocier une adresse IP publique, dans la section **Présentation**, cliquez sur **Dissocier**.

>[!WARNING]
>Lorsque vous modifiez la méthode d’affectation pour passer des adresses statiques à des adresses dynamiques, vous perdez l’adresse IP qui a été affectée à l’adresse IP publique. Si les serveurs DNS publics Azure conservent un mappage entre les adresses statiques ou dynamiques et une étiquette de nom DNS (si vous en avez défini une), une adresse IP dynamique peut être modifiée au démarrage de la machine virtuelle après qu’elle a été arrêtée (libérée). Pour empêcher toute modification de l’adresse, affectez une adresse IP statique.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) pour répertorier les adresses IP publiques, [az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) pour afficher les paramètres, [az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) pour mettre à jour, [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) pour supprimer|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) pour récupérer un objet d’adresse IP publique et afficher ses paramètres, [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) pour mettre à jour les paramètres, [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) pour supprimer|

## <a name="next-steps"></a>Étapes suivantes
Affectez des adresses IP publiques lors de la création de ressources Azure suivantes :

- Machines virtuelles [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Load Balancer accessible sur Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Connexion site à site à l’aide d’une passerelle VPN Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Groupe de machines virtuelles identiques Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

