---
title: "Ajouter ou supprimer des cartes réseau de machines virtuelles Azure | Documents Microsoft"
description: "Découvrez comment ajouter ou supprimer des interfaces réseau de machines virtuelles."
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
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 4273a8ec344c83d36d3383f991d1764d2285beeb
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---

# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Ajouter ou supprimer des cartes réseau de machines virtuelles

Découvrez comment ajouter une interface réseau existante lors de la création d’une machine virtuelle, ou ajouter ou supprimer des interfaces réseau d’une machine virtuelle existante à l’état arrêté (désalloué). Une interface réseau permet à une machine virtuelle Azure de communiquer avec des ressources sur Internet, sur Azure et locales. Une machine virtuelle peut avoir une ou plusieurs interfaces réseau. 

Si vous avez besoin d’ajouter, de modifier ou de supprimer des adresses IP pour une interface réseau, voir [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md). Si vous avez besoin de créer, modifier ou supprimer des interfaces réseau, consultez l’article [Gérer les interfaces réseau](virtual-network-network-interface.md).

## <a name="before"></a>Avant de commencer

Avant d’effectuer une étape de cet article, quelle que soit sa section, effectuez les tâches suivantes :

- Pour en savoir plus sur le nombre d’interfaces réseau que chaque taille de machine virtuelle Linux et Windows prend en charge, voir les articles sur les tailles de machine virtuelle [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Connectez-vous au portail Azure, à Azure CLI ou à Azure PowerShell avec un compte Azure. Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, installez et configurez Azure PowerShell en suivant les étapes décrites dans l’article [Comment installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell installées sont celles de la version la plus récente. Pour obtenir de l’aide sur les commandes PowerShell ainsi que des exemples, entrez `get-help <command> -full`.
- Si vous utilisez des commandes d’Azure CLI pour accomplir des tâches décrites dans cet article, installez et configurez Azure CLI en procédant de la manière décrite dans l’article [Comment installer et configurer Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`.

## <a name="about"></a>À propos des interfaces réseau et des machines virtuelles

Vous pouvez ajouter (attacher) une interface réseau existante à une machine virtuelle lorsque vous créez celle-ci, pour autant que l’interface réseau ne soit pas actuellement attachée à une autre machine virtuelle. Vous pouvez ajouter ou supprimer (détacher) une interface réseau de machine virtuelle existante, pour autant que celle-ci soit à l’état arrêté (désalloué). Si vous créez une machine virtuelle via le portail Azure, celui-ci crée pour vous une interface réseau avec des paramètres par défaut. Le portail ne vous autorise pas à :

- Spécifier une interface réseau existante à ajouter lors de la création de la machine virtuelle
- Créer une machine virtuelle avec plusieurs interfaces réseau
- Spécifiez le nom de l’interface réseau (le portail crée l’interface réseau avec un nom par défaut)

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour créer une machine virtuelle ou une interface réseau avec tous les attributs précédents pour lesquels vous ne pouvez pas utiliser le portail. Avant d’effectuer les tâches décrites dans les sections ci-après, tenez compte des contraintes et des comportements suivants :

- Toutes les tailles de machine virtuelle prennent en charge au moins deux interfaces réseau, mais certaines en prennent en charge plus de deux. Dans le passé, certaines tailles de machine virtuelle ne prenaient en charge qu’une seule interface réseau. Pour découvrir le nombre d’interfaces réseau prises en charge par chaque taille de machine virtuelle, lisez les articles sur les tailles des machines virtuelles [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Auparavant, les interfaces réseau ne pouvaient être ajoutées qu’aux machines virtuelles prenant en charge plusieurs interfaces réseau et créées avec au moins deux interfaces réseau. Il n’était pas possible d’ajouter une interface réseau à une machine virtuelle créée avec une seule interface réseau, même si la taille de machine virtuelle prenait en charge plusieurs interfaces réseau. À l’inverse, vous pouviez uniquement supprimer des interfaces réseau à partir d’une machine virtuelle comportant au moins trois interfaces réseau, car les machines virtuelles créées avec au moins deux interfaces réseau devaient toujours avoir au moins deux interfaces réseau. Ces contraintes sont aujourd’hui obsolètes. Vous pouvez maintenant créer une machine virtuelle avec un nombre quelconque d’interfaces réseau (pour autant que ce nombre soit pris en charge par la taille de machine virtuelle) et ajouter ou supprimer un nombre quelconque d’interfaces réseau (de machines virtuelles en état arrêté (désalloué)), pour autant qu’au moins une interface reste attachée à la machine virtuelle.
- Par défaut, la première interface réseau d’une machine virtuelle est définie comme l’interface réseau *principale*. Toutes les autres interfaces réseau de la machine virtuelle sont des interfaces réseau *secondaires*.
- Les serveurs DHCP Azure affectent une passerelle par défaut aux interfaces réseau principales, mais pas aux interfaces réseau secondaires. Étant donné qu’aucune passerelle par défaut n’est affectée aux interfaces réseau secondaires, par défaut, celles-ci ne peuvent pas communiquer avec des ressources situées hors de leur sous-réseau. Pour activer les interfaces réseau secondaires dans une machine virtuelle Windows afin de communiquer avec des ressources situées hors de leur sous-réseau, ajoutez des itinéraires au système d’exploitation en utilisant la commande `route add` à partir d’une ligne de commande Windows. Pour les machines virtuelles Linux, étant donné que le comportement par défaut utilise un routage d’hôte faible, nous recommandons de limiter le trafic pour les interfaces réseau secondaires à un seul sous-réseau. Si vous avez besoin de connectivité en dehors du sous-réseau pour des interfaces réseau secondaires, activez le routage basé sur des stratégies pour vous assurer que les trafics entrant et sortant utilisent la même interface réseau.
- Par défaut, tout le trafic sortant de la machine virtuelle est envoyé à l’adresse IP affectée à la configuration IP principale de l’interface réseau principale. Vous contrôlez l’adresse IP qui est utilisée pour le trafic sortant à l’intérieur du système d’exploitation de la machine virtuelle mais, par défaut, il transite par l’interface réseau principale.
- Auparavant, toutes les machines virtuelles du même groupe à haute disponibilité étaient requises pour une seule ou plusieurs interfaces réseau. Des machines virtuelles comportant un nombre quelconque d’interfaces réseau peuvent désormais exister dans le même groupe à haute disponibilité, pour autant que ce nombre soit pris en charge par la taille de la machine virtuelle. Cependant, vous ne pouvez ajouter une machine virtuelle à un groupe à haute disponibilité qu’au moment de la création de celui-ci. Pour en savoir plus sur les groupes à haute disponibilité, voir [Gérer la disponibilité des machines virtuelles dans Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Si des interfaces réseau attachées à la même machine virtuelle peuvent être connectées à différents sous-réseaux d’un réseau virtuel, les interfaces réseau doivent toutes être connectées au même réseau virtuel.
- Vous pouvez ajouter n’importe quelle adresse IP pour n’importe quelle configuration IP d’une interface réseau principale ou secondaire à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de l’interface réseau principale pouvait être ajoutée à un pool principal. Pour en savoir plus sur les configurations et les adresses IP, voir [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md).
- La suppression d’une machine virtuelle n’a pas pour effet de supprimer les interfaces réseau qui y sont attachées. Lorsqu’une machine virtuelle est supprimée, les interfaces réseau sont détachées de la machine virtuelle. Vous pouvez attacher les interfaces réseau à différentes machines virtuelles, ou les supprimer de celles-ci.

## <a name="vm-create"></a>Ajouter des interfaces réseau existantes à une nouvelle machine virtuelle
Lorsque vous créez une machine virtuelle via le portail, celui-ci crée une interface réseau avec des paramètres par défaut, et l’attache à la machine virtuelle pour vous. Vous ne pouvez pas ajouter d’interfaces réseau existantes à une nouvelle machine virtuelle, ou créer une machine virtuelle avec plusieurs interfaces réseau via le portail Azure. Vous pouvez en revanche effectuer les deux opérations à l’aide de l’interface de ligne de commande ou de PowerShell. Vous pouvez ajouter à une machine virtuelle autant d’interfaces réseau que le permet la taille de la machine virtuelle que vous créez. Pour découvrir le nombre d’interfaces réseau prises en charge par chaque taille de machine virtuelle, lisez les articles sur les tailles des machines virtuelles [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Actuellement, les interfaces réseau que vous ajoutez à une machine virtuelle ne peuvent pas être attachées à une autre machine virtuelle. Pour en savoir plus sur la création, la modification ou la suppression d’interfaces réseau, consultez l’article [Gérer les interfaces réseau](virtual-network-network-interface.md#create-nic).

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Ajouter une interface réseau existante à une machine virtuelle existante

Vous pouvez ajouter à une machine virtuelle autant d’interfaces réseau que le permet la taille de ladite machine virtuelle. Pour découvrir le nombre d’interfaces réseau prises en charge par chaque taille de machine virtuelle, lisez les articles sur les tailles des machines virtuelles [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). La machine virtuelle à laquelle vous souhaitez ajouter une interface réseau doit prendre en charge plusieurs interfaces réseau et se trouver à l’état arrêté (désalloué). Actuellement, les interfaces réseau que vous voulez ajouter ne peuvent pas être attachées à une autre machine virtuelle. Vous ne pouvez pas ajouter d’interface réseau à une machine virtuelle existante via le portail Azure. Pour ajouter des interfaces réseau à une machine virtuelle existante, vous devez utiliser l’interface de ligne de commande ou PowerShell.

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Instructions détaillées pour [ajouter une interface réseau à une machine virtuelle](../virtual-machines/windows/multiple-nics.md#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a> Afficher les interfaces réseau d’une machine virtuelle

Vous pouvez afficher les interfaces réseau actuellement attachées à une machine virtuelle pour découvrir la configuration de chaque interface réseau et l’adresse IP qui lui est attribuée. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant des autorisations associées au rôle Propriétaire, Collaborateur ou Collaborateur de réseau pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *machines virtuelles*. Lorsque la mention **machines virtuelles** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Machines virtuelles** qui s’affiche, cliquez sur le nom de la machine virtuelle pour laquelle vous souhaitez afficher les interfaces réseau.
4. Dans la section **PARAMÈTRES** du panneau Machine virtuelle qui s’affiche pour la machine virtuelle sélectionnée, cliquez sur **Interfaces réseau**. Pour en savoir plus sur les paramètres d’interface réseau et comment les modifier, consultez l’article [Gérer les interfaces réseau](virtual-network-network-interface.md). Pour en savoir plus sur l’ajout, la modification ou la suppression d’adresses IP d’une interface réseau, voir [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md).

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Supprimer des interfaces réseau d’une machine virtuelle

La machine virtuelle dont vous souhaitez supprimer une interface réseau doit se trouver à l’état arrêté (désalloué) et au moins deux interfaces réseau doivent y être attachées. Vous pouvez supprimer n’importe quelle interface réseau, mais au moins une interface réseau doit toujours être attachée à la machine virtuelle. Si vous supprimez une interface réseau principale, Azure affecte l’attribut principal à l’interface réseau restée attachée à la machine virtuelle le plus longtemps. Vous pouvez désigner vous-même toute interface réseau en tant qu’interface réseau principale. Vous ne pouvez pas supprimer d’interfaces réseau d’une machine virtuelle ou définir l’attribut principal pour une interface réseau via le portail Azure. En revanche, ces deux opérations sont possibles à l’aide de l’interface de ligne de commande ou de PowerShell. 

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Instructions détaillées pour [supprimer une interface réseau d’une machine virtuelle](../virtual-machines/windows/multiple-nics.md#remove-a-nic-from-an-existing-vm)|
## <a name="next-steps"></a>Étapes suivantes
Pour créer une machine virtuelle avec plusieurs interfaces réseau ou adresses IP, lisez les articles suivants :

**Commandes**

|Tâche|Outil|
|---|---|
|Créer une machine virtuelle avec plusieurs cartes d’interface réseau|[INTERFACE DE LIGNE DE COMMANDE](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Créer une machine virtuelle à carte réseau unique avec plusieurs adresses IP|[INTERFACE DE LIGNE DE COMMANDE](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

