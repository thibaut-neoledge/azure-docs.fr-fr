---
title: "Interfaces réseau Azure | Microsoft Docs"
description: "Découvrez comment créer et supprimer des interfaces réseau et affecter des adresses IP publiques et privées aux cartes réseau. Découvrez comment attacher et détacher des cartes réseau à partir de machines virtuelles Azure."
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
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: f691f3886fce217ea784237f03a4f02ed58e12ee
ms.lasthandoff: 03/28/2017


---

# <a name="network-interfaces"></a>Interfaces réseau

Découvrez les interfaces réseau et apprenez à les utiliser. Une carte réseau désigne l’interconnexion entre une machine virtuelle Azure et le réseau logiciel sous-jacent. Le schéma suivant illustre les fonctionnalités d’une carte réseau :

![Interface réseau](./media/virtual-network-network-interface/nic.png)

Cet article explique comment se servir des concepts indiqués dans le schéma. Cliquez sur l’un des concepts suivants pour accéder directement à la section correspondante dans l’article :

- [Interfaces réseau](#nics) : une carte réseau est connectée à un sous-réseau dans un réseau virtuel Azure (VNet). Dans le schéma, deux cartes réseau sont attachées à **VM1**, tandis qu’une carte réseau est attachée à **VM1**. Chaque carte réseau est connectée au même réseau virtuel, mais à des sous-réseaux différents. Cette section décrit les étapes permettant de répertorier les cartes réseau existantes, et de créer, modifier et supprimer des cartes réseau.
- [Configurations IP](#ip-configs) : une ou plusieurs configurations IP sont associées à chaque carte réseau. Une adresse IP privée est affectée à chaque configuration IP. Une adresse IP publique peut être associée à une configuration IP. Dans le schéma, une configuration IP est associée à **NIC1** et une autre configuration IP est associée à **NIC3**, tandis deux configurations IP sont associées à **NIC2**. Des adresses IP publiques sont affectées à la configuration IP affectée à NIC1 et NIC3, tandis qu’aucune adresse IP publique n’est affectée à une configuration d’adresse IP affectée à NIC2. Cette section décrit les étapes de création, de modification et de suppression des configurations IP auxquelles des adresses IP privées sont affectées à l’aide de méthodes d’affectation statiques et dynamiques. Cette section décrit également les étapes requises pour associer et dissocier des adresses IP publiques vis-à-vis d’une configuration IP.
- [Groupes de sécurité réseau](#nsgs) : les groupes de sécurité réseau contiennent une ou plusieurs règles de sécurité entrantes ou sortantes. Ces règles contrôlent le type de trafic réseau qui peut entrer dans une interface réseau et/ou un sous-réseau, et en sortir. Dans le schéma, un groupe de sécurité réseau est associé à **NIC1** et à **NIC3**, mais pas à **NIC2**. Cette section explique comment afficher les groupes de sécurité réseau appliqués à une carte réseau, comment ajouter un groupe de sécurité réseau à une carte réseau et comment supprimer un groupe de sécurité réseau à partir d’une carte réseau.
- [Machines virtuelles](#vms) : au moins une carte réseau est attachée à une machine virtuelle, mais plusieurs cartes réseau peuvent être attachées, selon la taille de la machine virtuelle. Pour connaître le nombre de cartes réseau pris en charge par chaque taille de machine virtuelle, consultez les articles sur les tailles des machines virtuelles [Windows](../virtual-machines/virtual-machines-windows-sizes.md) ou [Linux](../virtual-machines/virtual-machines-linux-sizes.md). Cette section explique comment créer des machines virtuelles avec une seule ou plusieurs cartes réseau, mais aussi comment attacher et détacher des cartes réseau sur des machines virtuelles existantes.

Si vous ne connaissez pas bien les cartes réseau et les machines virtuelles Azure, nous vous recommandons de réaliser l’exercice de la page [Créer votre premier réseau virtuel Azure](virtual-network-get-started-vnet-subnet.md) avant de lire cet article. Cet exercice vous familiarisera avec les machines virtuelles et les réseaux virtuels.

Cet article s’applique aux machines virtuelles et aux cartes réseau créées à l’aide du modèle de déploiement Azure Resource Manager. Microsoft recommande la création de ressources par le biais du modèle de déploiement Resource Manager, plutôt que du modèle de déploiement classique. Consultez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour en savoir plus sur les différences entre les deux modèles.

Les sections suivantes de cet article décrivent les étapes permettant d’effectuer toutes les tâches liées aux cartes réseau. Chaque section comporte :
- La procédure permettant d’effectuer la tâche dans le portail Azure. Pour effectuer toutes les étapes, vous devez ouvrir une session sur le [portail Azure](http://portal.azure.com). Si vous ne possédez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Les commandes permettant d’effectuer la tâche à l’aide d’Azure PowerShell ainsi que des liens vers les références de commandes. Installez et configurez PowerShell en suivant les instructions de l’article [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Pour obtenir de l’aide sur les commandes PowerShell ainsi que des exemples, entrez `get-help <command> -full`.
- Les commandes permettant d’effectuer la tâche à l’aide de l’interface de ligne de commande Azure (CLI) avec des liens vers les références de commandes. Installez Azure CLI en suivant les étapes décrites dans l’article [How to Install and Configure the Azure CLI 2.0 (Installation et configuration d’Azure CLI 2.0)](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> -h`.

## <a name="nics"></a>Interfaces réseau
Effectuez les étapes décrites dans les sections suivantes pour créer, afficher, modifier et supprimer les interfaces réseau et les paramètres :

### <a name="create-nic"></a>Créer une interface réseau

Une carte réseau peut être attachée à une machine virtuelle ou exister par elle-même. Pour savoir comment attacher une carte réseau à une machine virtuelle, reportez-vous à la section [Attacher une carte réseau à une machine virtuelle existante](#vm-attach-nic) de cet article.

Pour créer une carte réseau, exécutez les étapes suivantes :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque **interfaces réseau** s’affiche dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur **+ Ajouter**.
4. Dans le panneau **Créer l’interface réseau** qui s’affiche, entrez ou sélectionnez des valeurs pour les paramètres suivants, puis cliquez sur **Créer** :

    |**Paramètre**|**Obligatoire ?**|**Détails**|
    |---|---|---|
    |**Nom**|Oui|Il est impossible de modifier le nom une fois que la carte réseau a été créée. Le nom doit être unique au sein du groupe de ressources que vous avez sélectionné. Consultez l’article [Conventions d’affectation de noms](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) pour obtenir des suggestions de noms.|
    |**Réseau virtuel**|Oui|Vous pouvez uniquement connecter une carte réseau à un réseau virtuel existant dans les mêmes emplacement et abonnement que la carte réseau. La machine virtuelle à laquelle la carte réseau est attachée doit également se trouver dans les mêmes emplacement et abonnement que la carte réseau. Si aucun réseau virtuel n’est répertorié, vous devez en créer un. Pour créer un réseau virtuel, suivez les étapes de l’article [Réseau virtuel](virtual-networks-create-vnet-arm-pportal.md). Une fois qu’une carte réseau est créée, vous ne pouvez plus modifier le réseau virtuel auquel elle est connectée.|
    |**Sous-réseau**|Oui|Sélectionnez un sous-réseau dans le réseau virtuel que vous avez sélectionné. Vous pouvez modifier le sous-réseau auquel la carte réseau est connectée après sa création.|
    |**Affectation d’adresses IP privées**|Oui| Une adresse IP privée est affectée par le serveur DHCP Azure à la carte réseau lors de sa création. Le serveur DHCP affecte une adresse disponible à partir de la plage d’adresses de sous-réseau définie pour le sous-réseau auquel vous connectez la carte réseau. **Dynamique :** Azure peut affecter une autre adresse à une carte réseau au démarrage de la machine virtuelle à laquelle elle est attachée après s’être trouvée à l’état Arrêté (Désalloué). L’adresse reste la même si la machine virtuelle est redémarrée sans être passée par l’état Arrêté (Désalloué). **Statique :** les adresses statiques ne changent pas à moins que vous ne les modifiiez ou que la carte réseau ne soit supprimée. Vous pouvez modifier la méthode d’affectation après la création de la carte réseau.|
    |**Groupe de sécurité réseau**|Non|Les groupes de sécurité réseau vous permettent de contrôler le flux du trafic réseau en direction et en provenance d’une carte réseau. Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Groupes de sécurité réseau](virtual-networks-nsg.md). Vous pouvez appliquer zéro ou un groupe de sécurité réseau à une carte réseau. Zéro ou un groupe de sécurité réseau peut également être appliqué au sous-réseau auquel la carte réseau est connectée. Lorsqu’un groupe de sécurité réseau est appliqué à une carte réseau et au sous-réseau connecté, des résultats inattendus peuvent se produire. Pour résoudre les problèmes de groupes de sécurité réseau appliqués aux cartes réseau, consultez l’article [Dépanner les groupes de sécurité réseau](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface).|
    |**Abonnement**|Oui| La machine virtuelle à laquelle vous attachez une carte réseau et le réseau virtuel auquel vous la connectez doivent se trouver dans le même abonnement.|
    |**Groupe de ressources**|Oui| La carte réseau peut se trouver dans le même groupe de ressources ou dans un groupe de ressources différent de celui de la machine virtuelle à laquelle vous l’attachez, ou du réseau virtuel auquel vous la connectez.|
    |**Emplacement**|Oui|La machine virtuelle à laquelle vous attachez une carte réseau et le réseau virtuel auquel vous la connectez doivent se trouver dans le même emplacement.|

Le portail Azure crée une configuration IP principale nommée **ipconfig1** avec une adresse IP privée dynamique et l’associe à la carte réseau que vous créez. Pour en savoir plus sur les configurations IP, reportez-vous à la section [Configurations IP](#ip-configs) de cet article. Vous ne pouvez pas spécifier le nom de la configuration IP créée par le portail, affecter une adresse IP privée statique ni affecter une adresse IP publique lors de la création de la carte réseau. Si vous créez la carte réseau à l’aide de PowerShell ou de l’interface CLI, vous pouvez spécifier le nom de la configuration IP ainsi qu’une adresse IP statique, et affecter une adresse IP publique. Vous pouvez modifier la méthode d’affectation d’adresse IP privée et le fait d’associer ou non une adresse IP publique à la carte réseau après la création de la carte réseau. Pour modifier les paramètres après la création d’une carte réseau, suivez les étapes de la section [Modifier une configuration IP](#change-ip-config) de cet article.

>[!Note]
> Azure affecte une adresse MAC à la carte réseau uniquement lorsque la carte réseau est attachée à une machine virtuelle et lorsque la machine virtuelle démarre pour la première fois. Vous ne pouvez pas spécifier l’adresse MAC qu’Azure affecte à la carte réseau. L’adresse MAC reste affectée à la carte réseau jusqu’à ce que la carte réseau soit supprimée ou que l’adresse IP privée affectée à la configuration IP principale de la carte réseau principale soit modifiée. Pour en savoir plus sur les configurations IP, reportez-vous à la section [Configurations IP](#ip-configs) de cet article.

|**Outil**|**Commande**|
|:---|:---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

### <a name="view-nics"></a>Afficher et modifier les interfaces réseau et les paramètres

Pour afficher et modifier les interfaces réseau et les paramètres, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque **interfaces réseau** s’affiche dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur la carte réseau que vous souhaitez afficher ou dont vous souhaitez modifier les paramètres.
4. Les paramètres suivants sont répertoriés dans le panneau qui s’affiche pour la carte réseau que vous avez sélectionnée :
    - **Vue d’ensemble :** fournit des informations sur la carte réseau, comme les adresses IP affectées, le réseau virtuel/sous-réseau auquel la carte réseau est connectée, et la machine virtuelle à laquelle la carte réseau est attachée (si elle est attachée à une machine virtuelle). L’illustration suivante montre les paramètres de vue d’ensemble d’une carte réseau nommée **mywebserver256** :   ![Vue d’ensemble de l’interface réseau](./media/virtual-network-network-interface/nic-overview.png)
    - **Configurations IP :** au moins une configuration IP est affectée à une carte réseau, mais plusieurs configurations IP peuvent être affectées à une même carte. Pour connaître le nombre maximal de configurations IP prises en charge pour une carte réseau, consultez l’article [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Chaque configuration IP dispose d’une adresse IP privée et éventuellement d’une adresse IP publique. Pour modifier ce qui est affiché, suivez les étapes des sections [Ajouter une configuration IP secondaire à une carte réseau](#create-ip-config), [Modifier une configuration IP](#change-ip-config) ou [Supprimer une configuration IP secondaire à partir d’une carte réseau](#delete-ip-config) de cet article.
    - **Serveurs DNS :** vous pouvez spécifier à quel serveur DNS une carte réseau est affectée par le biais des serveurs DHCP Azure. Choisissez le serveur DNS interne Azure ou un serveur DNS personnalisé. Pour modifier ce qui est affiché, suivez les étapes de la section [Modifier les paramètres DNS d’une carte réseau](#dns) de cet article.
    - **Groupe de sécurité réseau :** indique si un groupe de sécurité réseau est associé à la carte réseau. Si un groupe de sécurité réseau est associé à la carte réseau, le nom du groupe de sécurité réseau associé s’affiche. Pour modifier ce qui est affiché, suivez les étapes de la section [Associer un groupe de sécurité réseau ou dissocier un groupe de sécurité réseau vis-à-vis d’une interface réseau](#associate-nsg) de cet article.
    - **Propriétés :** affiche les paramètres clés de la carte réseau afin d’inclure son adresse MAC et l’abonnement auquel elle appartient. Vous pouvez déplacer une carte réseau vers un autre groupe de ressources ou un autre abonnement, à condition de déplacer également toutes les ressources liées à la carte réseau. Si la carte réseau est attachée à une machine virtuelle, par exemple, vous devez également déplacer la machine virtuelle et toutes les ressources supplémentaires qui lui sont associées. Pour déplacer une carte réseau, consultez l’article [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Cet article dresse la liste des conditions préalables requises et explique comment déplacer des ressources à l’aide du portail Azure, de PowerShell et de l’interface Azure CLI.
    - **Obtenir les règles de sécurité effectives :** les règles de sécurité sont répertoriées si la carte réseau est attachée à une machine virtuelle en cours d’exécution, tandis qu’un groupe de sécurité réseau est associé à la carte réseau et/ou au sous-réseau auquel il est connecté. Pour en savoir plus sur ce qui est affiché, consultez l’article [Résoudre les problèmes relatifs aux groupes de sécurité réseau](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface). Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Groupes de sécurité réseau](virtual-networks-nsg.md).
    - **Obtenir les routages effectifs :** les routages sont répertoriés si la carte réseau est attachée à une machine virtuelle en cours d’exécution. Les routages sont une combinaison de routages par défaut Azure, de tous les routages définis par l’utilisateur et de tous les routages BGP susceptibles d’exister pour le sous-réseau auquel la carte réseau est connectée. Pour en savoir plus sur ce qui est affiché, consultez l’article [Résoudre les problèmes relatifs aux itinéraires](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez l’article [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).
    - **Paramètres courants d’Azure Resource Manager :** pour en savoir plus sur les paramètres courants d’Azure Resource Manager, consultez les articles [Journal d’activité](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Contrôle d’accès (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Balises](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Verrous](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Script Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) pour afficher les cartes réseau dans l’abonnement ; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) pour afficher les paramètres d’une carte réseau|
|**PowerShell**|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) pour afficher les cartes réseau dans l’abonnement ou pour afficher les paramètres d’une carte réseau|

### <a name="dns"></a>Modifier les paramètres DNS d’une carte réseau

Pour modifier les paramètres DNS d’une carte réseau, procédez comme suit. Le serveur DNS est affecté à la machine virtuelle par le serveur DHCP Azure. Pour en savoir plus sur les paramètres de résolution de noms d’une carte réseau, consultez l’article [Résolution de noms pour les machines virtuelles](virtual-networks-name-resolution-for-vms-and-role-instances.md).

1. Effectuez les étapes 1 à 3 dans la section [Afficher et modifier les interfaces réseau et les paramètres](#view-nics) de cet article pour la carte réseau dont vous souhaitez modifier les paramètres.
2. Dans le panneau de la carte réseau que vous avez sélectionnée, cliquez sur **Serveurs DNS**.
3. Cliquez sur l’une des deux options suivantes :
    - **Hériter de VNet (par défaut)**: choisissez cette option afin d’hériter le paramètre de serveur DNS défini pour le réseau virtuel auquel la carte réseau est connectée. Au niveau du réseau virtuel, soit un serveur DNS personnalisé, soit le serveur DNS fourni par Azure est défini. Le serveur DNS fourni par Azure peut résoudre les noms des ressources connectées au même réseau virtuel, mais pas ceux des ressources connectées à différents réseaux virtuels.
    - **Personnalisé** : vous pouvez configurer votre propre serveur DNS pour résoudre les noms sur plusieurs réseaux virtuels. Entrez l’adresse IP du serveur que vous souhaitez utiliser comme serveur DNS. L’adresse de serveur DNS que vous spécifiez est affectée uniquement à cette carte réseau et remplace tous les paramètres DNS du réseau virtuel auquel la carte réseau est connectée.
4. Cliquez sur **Save**.

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="ip-forwarding"></a>Modifier le transfert IP pour une carte réseau

Le transfert IP permet à la machine virtuelle à laquelle une carte réseau est attachée de :
- Recevoir le trafic réseau qui n’est pas destiné à l’une des adresses IP affectées à l’une des configurations IP affectées à la carte réseau.
- Envoyer le trafic réseau avec une adresse IP source différente de celle affectée à l’une de ses configurations IP.

Ce paramètre doit être activé pour chaque carte réseau attachée à la machine virtuelle recevant le trafic que la machine virtuelle doit transférer. Une machine virtuelle peut transférer le trafic, qu’une ou plusieurs cartes réseau lui soient attachées. Bien que le transfert IP soit un paramètre Azure, la machine virtuelle doit également exécuter une application capable de transférer le trafic, comme une application de pare-feu, d’optimisation WAN et d’équilibrage de charge. Lorsqu’une machine virtuelle exécute des applications réseau, la machine virtuelle est communément appelée appliance virtuelle réseau (NVA). Vous pouvez afficher une liste de NVA prêtes au déploiement sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Le transfert IP est généralement utilisé avec les routages définis par l’utilisateur. Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez l’article [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).

Pour modifier les paramètres de transfert IP d’une carte réseau, procédez comme suit :

1. Effectuez les étapes 1 à 3 dans la section [Afficher et modifier les interfaces réseau et les paramètres](#view-nics) de cet article pour la carte réseau que vous souhaitez modifier.
2. Dans le panneau de la carte réseau que vous avez sélectionnée, cliquez sur Configurations IP
3. Cliquez sur **Activé** ou **Désactivé** (paramètre par défaut) pour modifier le paramètre.
4. Cliquez sur **Save**.

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="subnet"></a>Modifier le sous-réseau auquel une carte réseau est connectée

Vous pouvez modifier le sous-réseau auquel une carte réseau est connectée, mais pas le réseau virtuel. Pour modifier le sous-réseau, des adresses IP privées dynamiques doivent être affectées à toutes les configurations IP associées à la carte réseau. Pour modifier le sous-réseau auquel une carte réseau est connectée, procédez comme suit :

1. Effectuez les étapes 1 à 3 dans la section [Afficher et modifier les interfaces réseau et les paramètres](#view-nics) de cet article pour la carte réseau que vous souhaitez connecter à un autre sous-réseau.
2. Dans le panneau de la carte réseau que vous avez sélectionnée, cliquez sur **Configurations IP**. Si les adresses IP privées sont affectées à toutes les configurations IP répertoriées avec une méthode statique, vous devez passer à une méthode dynamique en procédant comme suit. Si les adresses sont affectées avec la méthode dynamique, passez à l’étape 3 :
    - Cliquez sur l’adresse IP statique de la configuration IP que vous souhaitez modifier dans la liste des configurations IP.
    - Dans le panneau qui s’affiche pour la configuration IP, cliquez sur **Dynamique** pour la méthode **Affectation**.
    - Cliquez sur **Save**.
3. Sélectionnez le sous-réseau auquel vous souhaitez connecter la carte réseau à partir de la liste déroulante **Sous-réseau**.
4. Cliquez sur **Save**. De nouvelles adresses dynamiques sont affectées à partir de la plage d’adresses de sous-réseau du réseau. Si vous le souhaitez, vous pouvez ensuite affecter des adresses IP statiques à partir de la nouvelle plage d’adresses de sous-réseau.

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="delete-nic"></a>Supprimer une interface réseau

Vous pouvez supprimer une carte réseau tant qu’elle n’est pas attachée à une machine virtuelle. Si elle est attachée à une machine virtuelle, vous devez la détacher de la machine virtuelle avant de pouvoir la supprimer. Pour détacher une carte réseau d’une machine virtuelle, suivez les étapes de la section [Détacher une carte réseau d’une machine virtuelle existante](#vm-detach-nic) de cet article.

1. Effectuez les étapes 1 et 2 dans la section [Afficher et modifier les interfaces réseau et les paramètres](#view-nics) de cet article pour la carte réseau que vous souhaitez supprimer.
2. Cliquez avec le bouton droit sur la carte réseau à supprimer, puis cliquez sur **Supprimer**.
3. Cliquez sur **Oui** pour confirmer la suppression de la carte réseau.

Lorsque vous supprimez une carte réseau, toutes les adresses MAC ou IP qui lui sont affectées sont libérées.

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configs"></a>Configurations IP
Chaque carte réseau dispose d’au moins une configuration IP, appelée configuration **principale**. Une carte réseau peut également avoir une ou plusieurs configurations IP *secondaires* qui lui sont associées. Le nombre d’adresses IP que vous pouvez affecter à une carte réseau est limité. Pour en savoir plus, consultez l’article [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Chaque configuration IP :
- Dispose d’une adresse IP privée qui lui est affectée à l’aide de la méthode d’affectation statique ou dynamique. Les adresses IP dynamiques peuvent changer si une machine virtuelle est démarrée après s’être trouvée à l’état Arrêté (Désalloué). Les adresses IP statiques sont libérées d’une carte réseau uniquement si la carte réseau est supprimée.
- Peut disposer d’une adresse IP publique unique qui lui est associée.

Les serveurs DHCP Azure affectent l’adresse IP privée de la configuration IP principale de la carte réseau à la carte réseau située dans le système d’exploitation de la machine virtuelle.

L’affectation d’adresses IP multiples à une carte réseau est notamment utile dans les scénarios suivants :
- D’héberger plusieurs sites web ou services avec des adresses IP différentes et des certificats SSL sur un serveur unique.
- Une machine virtuelle joue le rôle d’appliance virtuelle réseau, comme un pare-feu ou un équilibreur de charge.
- La possibilité d’ajouter l’une des adresses IP privées pour toutes les cartes réseau à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de la carte réseau principale pouvait être ajoutée à un pool principal. Pour plus d’informations sur la façon d’équilibrer la charge entre plusieurs configurations IP, consultez l’article [Équilibrage de la charge entre plusieurs configurations IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Un nombre limité d’adresses IP publiques peut être utilisé dans un abonnement, et un nombre limité d’adresses IP privées peut être affecté à une carte réseau. Pour en savoir plus sur ces limites, consultez l’article [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="create-ip-config"></a>Ajouter une configuration IP secondaire à une carte réseau

Vous pouvez ajouter autant de configurations IP que nécessaire à une carte réseau, dans les limites indiquées dans l’article [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Pour ajouter une configuration IP à une carte réseau, procédez comme suit :

1. Effectuez les étapes 1 à 3 dans la section [Afficher et modifier les interfaces réseau et les paramètres](#view-nics) de cet article pour la carte réseau à laquelle vous souhaitez ajouter une configuration IP.
2. Dans le panneau de la carte réseau que vous avez sélectionnée, cliquez sur **Configurations IP**.
3. Cliquez sur **+ Ajouter** dans le panneau qui s’ouvre pour les configurations IP.
4. Indiquez les éléments suivants, puis cliquez sur **OK** pour fermer le panneau **Ajouter une configuration IP** :

    |**Paramètre**|**Obligatoire ?**|**Détails**|
    |---|---|---|
    |**Nom**|Oui|Doit être unique pour la carte réseau|
    |**Type**|Oui|Étant donné que vous ajoutez une configuration IP à une carte réseau existante et que chaque carte réseau doit disposer d’une configuration IP principale, la seule option possible est **Secondaire**.|
    |**Méthode Affectation d’adresses IP privées**|Oui|Les adresses **dynamiques** peuvent être modifiées si la machine virtuelle est redémarrée sans passer par l’état Arrêté (Désalloué). Les adresses **statiques** ne sont pas libérées si la carte réseau n’est pas supprimée. Spécifiez une adresse IP à partir de la plage d’espace d’adressage du sous-réseau, qui n’est pas actuellement utilisée par une autre configuration IP.|
    |**Adresse IP publique**|Non|**Désactivée :** aucune ressource d’adresse IP publique n’est actuellement associée à la configuration IP. **Activée :** sélectionnez une adresse IP publique existante ou créez-en une. Pour savoir comment créer une adresse IP publique, consultez l’article [Adresses IP publiques](virtual-network-public-ip-address.md#create).|
5. Ajoutez manuellement des adresses IP privées secondaires au système d’exploitation de la machine virtuelle en suivant les instructions de l’article [Affecter plusieurs adresses IP aux machines virtuelles](virtual-network-multiple-ip-addresses-portal.md#os-config). N’ajoutez pas d’adresse IP publique au système d’exploitation de la machine virtuelle.

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="change-ip-config"></a>Modifier une configuration IP

Pour modifier les paramètres d’adresses IP privées et publiques d’une configuration IP principale ou secondaire, procédez comme suit :

1. Effectuez les étapes 1 à 3 dans la section [Afficher et modifier les interfaces réseau et les paramètres](#view-nics) de cet article pour la carte réseau que vous souhaitez modifier.
2. Dans le panneau de la carte réseau que vous avez sélectionnée, cliquez sur **Configurations IP**.
3. Cliquez sur la configuration IP que vous souhaitez modifier dans la liste du panneau qui s’affiche pour les configurations IP.
4. Modifiez les paramètres en utilisant les informations relatives aux paramètres de la section [Ajouter une configuration IP secondaire à une carte réseau](#create-ip-config) de cet article, puis cliquez sur **Enregistrer** pour fermer le panneau de la configuration IP que vous avez sélectionnée.

>[!NOTE]
>Si la carte réseau principale dispose de plusieurs configurations IP et si vous modifiez l’adresse IP privée de la configuration IP principale, vous devez réaffecter manuellement toutes les adresses IP secondaires à la carte réseau dans Windows (non requis pour Linux). Pour affecter manuellement des adresses IP à une carte réseau au sein d’un système d’exploitation, consultez l’article [Affecter plusieurs adresses IP aux machines virtuelles](virtual-network-multiple-ip-addresses-portal.md#os-config). N’ajoutez pas d’adresse IP publique au système d’exploitation de la machine virtuelle.

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="delete-ip-config"></a>Supprimer une configuration IP secondaire à partir d’une carte réseau

Procédez comme suit pour supprimer une configuration IP secondaire à partir d’une carte réseau :

1. Effectuez les étapes 1 à 3 dans la section [Afficher et modifier les interfaces réseau et les paramètres](#view-nics) de cet article pour la carte réseau que vous souhaitez modifier.
2. Dans le panneau de la carte réseau que vous avez sélectionnée, cliquez sur **Configurations IP**.
3. Cliquez avec le bouton droit sur la configuration IP secondaire que vous souhaitez supprimer, puis cliquez sur **Supprimer**. Si une ressource d’adresse IP publique est associée à la configuration, la ressource est dissociée de la configuration IP, mais la ressource n’est pas supprimée.
4. Fermez le panneau **Configurations IP**.

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="nsgs"></a>Groupes de sécurité réseau
Un groupe de sécurité réseau contient une liste des règles entrantes et sortantes qui autorisent ou rejettent le trafic réseau vers une carte réseau. Il n’est pas nécessaire d’associer un groupe de sécurité réseau à une carte réseau et au sous-réseau auquel la carte réseau est connectée. Toutefois, un groupe de sécurité réseau peut être associé à une carte réseau et/ou au sous-réseau auquel la carte réseau est connectée. Les groupes de sécurité réseau dont les règles s’appliquent à toutes les cartes réseau connectées au sous-réseau sont généralement associés aux sous-réseaux. Un groupe de sécurité réseau avec des règles plus granulaires peut ensuite être appliqué à des cartes réseau individuelles. Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Groupes de sécurité réseau](virtual-networks-nsg.md).

### <a name="associate-nsg"></a>Associer un groupe de sécurité réseau ou dissocier un groupe de sécurité réseau vis-à-vis d’une interface réseau

Pour associer ou dissocier un groupe de sécurité réseau vis-à-vis d’une carte réseau, procédez comme suit :

1. Effectuez les étapes 1 à 3 dans la section [Afficher et modifier les interfaces réseau et les paramètres](#view-nics) de cet article pour la carte réseau vis-à-vis de laquelle vous souhaitez associer ou dissocier un groupe de sécurité réseau.
2. Dans le panneau de la carte réseau que vous avez sélectionnée, cliquez sur **Groupe de sécurité réseau**. Un panneau s’affiche avec l’option **Modifier** en haut. Si aucun groupe de sécurité réseau n’est actuellement associé à la carte réseau, l’option **Groupe de sécurité réseau** *Aucun* s’affiche. Si un groupe de sécurité réseau est actuellement associé à une carte réseau, l’option **Groupe de sécurité réseau** *Nom-GroupeSécuritéRéseau* (où Nom-GroupeSécuritéRéseau désigne le nom du groupe de sécurité réseau actuellement associé à la carte réseau) s’affiche.
3. Cliquez sur **Modifier**.
4. Cliquez sur **Groupe de sécurité réseau**. Si aucun groupe de sécurité réseau n’est répertorié, cela signifie que votre abonnement n’en contient aucun. Pour créer un groupe de sécurité réseau, suivez la procédure indiquée dans l’article [Groupes de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md).
5. Dans le panneau **Choisir un groupe de sécurité réseau** qui s’affiche, cliquez sur un groupe de sécurité réseau existant dans la liste afin d’associer ce groupe de sécurité réseau à la carte réseau, ou cliquez sur **Aucun**pour dissocier un groupe de sécurité réseau actuellement associé à une carte réseau.
6. Cliquez sur **Save**.

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vms"></a>Attacher et détacher des cartes réseau vis-à-vis d’une machine virtuelle

Vous pouvez attacher une carte réseau existante à une machine virtuelle au moment de sa création, ou vous pouvez attacher une carte réseau existante à une machine virtuelle existante. Vous pouvez également détacher une carte réseau d’une machine virtuelle existante disposant d’au moins deux cartes réseau. Bien que le portail crée une carte réseau lorsque vous créez une machine virtuelle, il ne vous permet pas de :

- Spécifier une carte réseau existante à attacher lors de la création de la machine virtuelle
- Créer une machine virtuelle à laquelle plusieurs cartes d’interface réseau sont attachées
- Attribuer un nom à la carte réseau (le portail crée la carte réseau avec un nom par défaut)
- Spécifier le caractère statique de la méthode d’affectation d’adresses IP privées. Le portail affecte automatiquement une adresse IP privée dynamique, mais vous pouvez modifier la méthode d’affectation une fois que le portail a créé la carte réseau.

Vous pouvez utiliser PowerShell ou l’interface CLI pour créer une machine virtuelle ou une carte réseau avec tous les attributs précédents pour lesquels que vous ne pouvez pas utiliser le portail. Avant d’effectuer les tâches décrites dans les sections ci-après, tenez compte des contraintes et des comportements suivants :

- Différentes tailles de machine virtuelle prennent en charge différents nombres de cartes réseau. Pour connaître le nombre de cartes réseau pris en charge par chaque taille de machine virtuelle, consultez les articles sur les tailles des machines virtuelles [Linux](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Par défaut, la première carte réseau attachée à une machine virtuelle est définie comme la carte réseau *principale*. Toutes les autres cartes réseau attachées à la machine virtuelle sont des cartes réseau *secondaires*.
- Par défaut, tout le trafic sortant de la machine virtuelle est envoyé à l’adresse IP affectée à la configuration IP principale de la carte réseau principale. Vous pouvez évidemment contrôler l’adresse IP utilisée pour le trafic sortant au sein du système d’exploitation de la machine virtuelle.
- Auparavant, toutes les machines virtuelles du même groupe à haute disponibilité étaient requises pour une seule ou plusieurs cartes réseau. Les machines virtuelles d’un même groupe à haute disponibilité peuvent désormais comporter n’importe quel nombre de cartes réseau. Cependant, une machine virtuelle ne peut être ajoutée à un groupe à haute disponibilité qu’au moment de sa création. Pour en savoir plus sur les groupes à haute disponibilité, consultez l’article [Gestion de la disponibilité des machines virtuelles Windows dans Azure](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Si les cartes réseau attachées à la même machine virtuelle peuvent être connectées à différents sous-réseaux d’un réseau virtuel, les cartes réseau doivent toutes être connectées au même réseau virtuel.
- Vous pouvez ajouter n’importe quelle adresse IP pour n’importe quelle configuration IP d’une carte réseau principale ou secondaire à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de la carte réseau principale pouvait être ajoutée à un pool principal.
- La suppression d’une machine virtuelle ne supprime pas les cartes réseau qui y sont attachées. Lorsqu’une machine virtuelle est supprimée, les cartes réseau sont détachées de la machine virtuelle. Vous pouvez attacher les cartes réseau à différentes machines virtuelles, ou les supprimer.

### <a name="vm-create"></a>Attacher une ou plusieurs cartes réseau lors de la création d’une machine virtuelle

Vous ne pouvez pas attacher de carte réseau existante à une nouvelle machine virtuelle, ni créer de machine virtuelle avec plusieurs cartes réseau à l’aide du portail Azure. Vous pouvez utiliser les commandes Azure CLI ou PowerShell suivantes pour attacher une ou plusieurs cartes existantes lors de la création d’une machine virtuelle :

- **CLI :** [az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)
- **PowerShell :** [New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-view-nic"></a>Afficher les cartes réseau attachées à une machine virtuelle

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant des autorisations associées au rôle Propriétaire, Collaborateur ou Collaborateur de réseau pour votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *machines virtuelles*. Lorsque **machines virtuelles** s’affiche dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Machines virtuelles** qui s’affiche, cliquez sur le nom de la machine virtuelle pour laquelle vous souhaitez afficher les interfaces réseau attachées.
4. Dans le panneau **Machine virtuelle** qui s’affiche pour la machine virtuelle sélectionnée, cliquez sur **Interfaces réseau**.

|**Outil**|**Commande**|
|---|---|
|**INTERFACE DE LIGNE DE COMMANDE**|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|**PowerShell**|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="vm-attach-nic"></a>Attacher une carte réseau à une machine virtuelle existante

La machine virtuelle à laquelle vous souhaitez attacher une carte réseau doit prendre en charge plusieurs cartes réseau et se trouver à l’état Arrêté (Désalloué). Vous ne pouvez pas attacher de carte réseau à une machine virtuelle existante à l’aide du portail Azure. Vous pouvez utiliser les commandes Azure CLI ou PowerShell suivantes pour attacher des cartes réseau aux machines virtuelles :

- **CLI :** [az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)
- **PowerShell :** [Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-detach-nic"></a>Détacher une carte réseau d’une machine virtuelle existante

La machine virtuelle dont vous souhaitez détacher une carte réseau doit se trouver à l’état Arrêté (Désalloué) et deux cartes réseau doivent y être attachées au minimum. Vous pouvez détacher n’importe quelle carte réseau, mais au moins une carte réseau doit être attachée à la machine virtuelle. Si vous détachez une carte réseau principale, Azure affecte l’attribut principal à la carte réseau qui est attachée à la machine virtuelle depuis le plus longtemps. Vous pouvez également désigner vous-même n’importe quelle carte réseau en tant que carte réseau principale. Vous ne pouvez pas détacher de cartes réseau d’une machine virtuelle ni définir l’attribut principal pour une carte réseau à l’aide du portail Azure, bien que ces deux opérations soient possibles par le biais de l’interface CLI ou de PowerShell. Vous pouvez utiliser les commandes Azure CLI ou PowerShell suivantes pour détacher des cartes réseau des machines virtuelles :

- **CLI :** [az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)
- **PowerShell :** [Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>Étapes suivantes
Pour créer une machine virtuelle avec plusieurs cartes réseau ou configurations IP à l’aide de scripts, consultez les articles suivants :

|**Tâche**|**Outil**|
|---|---|
|**Créer une machine virtuelle avec plusieurs cartes d’interface réseau**|[CLI](virtual-network-deploy-multinic-arm-cli.md) et [PowerShell](virtual-network-deploy-multinic-arm-ps.md)|
|**Créer une machine virtuelle avec carte réseau unique pour laquelle plusieurs adresses IP sont affectées**|[CLI](virtual-network-multiple-ip-addresses-cli.md) et [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

