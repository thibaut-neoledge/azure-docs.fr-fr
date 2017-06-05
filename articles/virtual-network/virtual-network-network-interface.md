---
title: "Créer, modifier ou supprimer des cartes réseau Azure | Documents Microsoft"
description: "Découvrez les cartes réseau (NIC) et comment les créer, en modifier les paramètres et les supprimer."
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
ms.openlocfilehash: f1fb0f6348b579121be64bff4411952026f8528d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017


---

# <a name="create-change-or-delete-network-interfaces"></a>Créer, modifier ou supprimer des cartes réseau

Découvrez comment créer, paramétrer et supprimer des cartes réseau (NIC). Une carte réseau permet à une machine virtuelle Azure de communiquer avec des ressources sur Internet, sur Azure et locales. Lorsque vous créez une machine virtuelle via le portail Azure, celui-ci crée pour vous une carte réseau avec des paramètres par défaut. À la place, vous pouvez créer des cartes réseau avec des paramètres personnalisés, et en ajouter une ou plusieurs à des machines virtuelles lors de la création de celles-ci. Vous pouvez également modifier les paramètres par défaut de cartes réseau existantes. Cet article explique comment créer des cartes réseau avec des paramètres personnalisés, modifier les paramètres d’une carte réseau (par exemple, l’affectation de filtre réseau pour les groupes de sécurité réseau, l’attribution de sous-réseau, les paramètres de serveur DNS et le transfert IP) ainsi que supprimer des cartes réseau. 

Si vous avez besoin d’ajouter, de modifier ou de supprimer des adresses IP pour une carte réseau, voir [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md). Si vous avez besoin d’ajouter ou de supprimer des cartes réseau de machines virtuelles, voir [Ajouter ou supprimer des cartes réseau](virtual-network-network-interface-vm.md). 


## <a name="before"></a>Avant de commencer

Avant de suivre les étapes décrites dans toute section de cet article, accomplissez les tâches suivantes :

- Pour découvrir les limites des cartes réseau, voir [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Connectez-vous au portail Azure, à Azure CLI ou à Azure PowerShell avec un compte Azure. Si vous n’avez pas encore de compte Azure, [créez votre compte Azure gratuit](https://azure.microsoft.com/free).
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, installez et configurez Azure PowerShell en suivant les étapes décrites dans l’article [Comment installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que les applets de commande Azure PowerShell installées sont celles de la version la plus récente. Pour obtenir de l’aide sur les commandes PowerShell ainsi que des exemples, entrez `get-help <command> -full`.
- Si vous utilisez des commandes d’Azure CLI pour accomplir des tâches décrites dans cet article, installez et configurez Azure CLI en procédant de la manière décrite dans l’article [Comment installer et configurer Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`.

## <a name="create-nic"></a>Créer une carte réseau
Lorsque vous créez une machine virtuelle via le portail Azure, celui-ci crée pour vous une carte réseau avec des paramètres par défaut. Si vous préférez spécifier tous les paramètres de votre carte réseau, vous pouvez créer une carte réseau avec des paramètres personnalisés, puis l’attacher à une machine virtuelle lors de la création de celle-ci. Vous pouvez également créer une carte réseau et l’ajouter à une machine virtuelle existante. Pour savoir comment créer une machine virtuelle avec une carte réseau existante, ou ajouter ou supprimer des cartes réseau de machines virtuelles existantes, voir [Ajouter ou supprimer des cartes réseau](virtual-network-network-interface-vm.md). Avant de créer une carte réseau, vous devez disposer d’un réseau virtuel dans les mêmes emplacement et abonnement que ceux dans lesquels vous créez la carte. Pour savoir comment créer un réseau virtuel, voir [Créer un réseau virtuel](virtual-networks-create-vnet-arm-pportal.md).

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur **+ Ajouter**.
4. Dans le panneau **Créer l’interface réseau** qui s’affiche, entrez ou sélectionnez des valeurs pour les paramètres suivants, puis cliquez sur **Créer**:

    |Paramètre|Requis ?|Détails|
    |---|---|---|
    |Nom|Oui|Le nom doit être unique au sein du groupe de ressources que vous avez sélectionné. Au fil du temps, vous accumulerez probablement plusieurs cartes réseau dans votre abonnement Azure. Pour obtenir des suggestions lors de la création d’une convention d’affectation de noms pour faciliter la gestion de plusieurs cartes réseau, voir [Conventions d’affectation de noms](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Il est impossible de modifier le nom une fois que la carte réseau a été créée.|
    |Réseau virtuel|Oui|Sélectionnez un réseau virtuel auquel connecter la carte réseau. Vous pouvez uniquement connecter une carte réseau à un réseau virtuel existant dans les mêmes emplacement et abonnement que la carte réseau. Une fois qu’une carte réseau est créée, vous ne pouvez plus modifier le réseau virtuel auquel elle est connectée. La machine virtuelle à laquelle vous ajoutez la carte réseau doit également se trouver dans les mêmes emplacement et abonnement que la carte réseau.|
    |Sous-réseau|Oui|Sélectionnez un sous-réseau dans le réseau virtuel que vous avez sélectionné. Vous pouvez modifier le sous-réseau auquel la carte réseau est connectée après sa création.|
    |Affectation d’adresses IP privées|Oui| Choisissez parmi les méthodes d’affectation suivantes : **Dynamique :** lorsque vous sélectionnez cette option, Azure affecte automatiquement une adresse disponible de l’espace d’adressage du sous-réseau sélectionné. Azure peut affecter une autre adresse à une carte réseau quand la machine virtuelle à laquelle elle est attachée démarre après avoir été arrêtée (désallouée). L’adresse reste la même si la machine virtuelle est redémarrée sans être passée par l’état Arrêté (Désalloué). **Statique :** lorsque vous sélectionnez cette option, vous devez affecter manuellement une adresse IP disponible de l’espace d’adressage du sous-réseau sélectionné. Les adresses statiques ne changent que si vous les modifiez ou si la carte réseau est supprimée. Vous pouvez modifier la méthode d’affectation après la création de la carte d’interface réseau. Le serveur DHCP Azure affecte cette adresse à la carte réseau au sein du système d’exploitation de la machine virtuelle.|
    |Groupe de sécurité réseau|Non| Conservez la valeur **Aucune**, puis sélectionnez un groupe de sécurité réseau (NSG) ou créez-en un. Les groupes de sécurité réseau vous permettent de filtrer le trafic réseau entrant et sortant d’une carte réseau. Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Groupes de sécurité réseau](virtual-networks-nsg.md). Pour créer un groupe de sécurité réseau, voir [Créer un groupe de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md). Vous pouvez appliquer zéro ou un groupe de sécurité réseau à une carte réseau. Zéro ou un groupe de sécurité réseau peut également être appliqué au sous-réseau auquel la carte réseau est connectée. Quand un groupe de sécurité réseau est appliqué à une carte réseau et au sous-réseau auquel la carte réseau est connectée, des résultats inattendus peuvent se produire. Pour résoudre les problèmes de groupes de sécurité réseau appliqués à des cartes réseau et à des sous-réseaux, voir [Dépanner les groupes de sécurité réseau](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface).|
    |Abonnement|Oui|Sélectionnez l’un de vos [abonnements](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure. La machine virtuelle à laquelle vous attachez une carte réseau et le réseau virtuel auquel vous la connectez doivent se trouver dans le même abonnement.|
    |Groupe de ressources|Oui|Sélectionnez un [groupe de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) ou créez-en un. Une carte réseau peut se trouver ou non dans le même groupe de ressources que celui de la machine virtuelle à laquelle vous l’attachez ou du réseau virtuel auquel vous la connectez.|
    |Lieu|Oui|La machine virtuelle à laquelle vous attachez une carte réseau et le réseau virtuel auquel vous la connectez doivent se trouver dans le même [emplacement](https://azure.microsoft.com/regions), également appelé région.|

Le portail n’offre pas la possibilité d’affecter une adresse IP publique à la carte réseau lors de la création de celle-ci, même s’il affecte une adresse IP publique à une carte réseau quand vous créez une machine virtuelle via le portail. Pour savoir comment ajouter une adresse IP publique à la carte réseau après création de celle-ci, voir [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md). Si vous souhaitez créer une carte réseau avec une adresse IP publique, vous devez utiliser l’interface de ligne de commande ou PowerShell pour la créer.

>[!Note]
> Azure affecte une adresse MAC à la carte réseau uniquement lorsque la carte réseau est attachée à une machine virtuelle et lorsque la machine virtuelle démarre pour la première fois. Vous ne pouvez pas spécifier l’adresse MAC qu’Azure affecte à la carte réseau. L’adresse MAC reste affectée à la carte réseau jusqu’à ce que la carte réseau soit supprimée ou que l’adresse IP privée affectée à la configuration IP principale de la carte réseau principale soit modifiée. Pour en savoir plus sur les adresses IP et les configurations IP, voir [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md).

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

## <a name="view-nics"></a>Afficher les paramètres de carte réseau

Vous pouvez afficher et modifier la plupart des paramètres d’une carte réseau.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur la carte réseau que vous souhaitez afficher ou dont vous souhaitez modifier les paramètres.
4. Les paramètres suivants sont répertoriés dans le panneau qui s’affiche pour la carte réseau que vous avez sélectionnée :
    - **Vue d’ensemble :** fournit des informations sur la carte réseau, telles que les adresses IP qui lui sont affectées, le réseau virtuel ou sous-réseau auquel elle est connectée, et la machine virtuelle à laquelle la carte réseau est attachée (le cas échéant). L’illustration suivante montre les paramètres de vue d’ensemble d’une carte réseau nommée **mywebserver256** :   ![Vue d’ensemble de l’interface réseau](./media/virtual-network-network-interface/nic-overview.png) Vous pouvez déplacer une carte réseau vers un autre groupe de ressources ou un abonnement en cliquant sur (**Modifier**) en regard du **Groupe de ressources** ou du **Nom d’abonnement**. Si vous déplacez la carte réseau, vous devez déplacer en même temps toutes les ressources liées à celle-ci. Par exemple, si la carte réseau est attachée à une machine virtuelle, vous devez également déplacer la machine virtuelle et toutes les autres ressources associées à celle-ci. Pour déplacer une carte réseau, consultez l’article [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Cet article répertorie les conditions préalables et explique comment déplacer des ressources à l’aide du portail Azure, de PowerShell ou d’Azure CLI.
    - **Configurations IP :** des adresses IP publiques et privées sont affectées à une ou plusieurs configurations IP pour une carte réseau. Pour connaître le nombre maximal de configurations IP prises en charge pour une carte réseau, voir [Limites d’Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Chaque configuration IP dispose d’une adresse IP privée et éventuellement d’une adresse IP publique. Pour ajouter, modifier ou supprimer des configurations IP de la carte réseau, procédez comme décrit dans les sections [Ajouter une configuration IP secondaire à une carte réseau](virtual-network-network-interface-addresses.md#create-ip-config), [Modifier une configuration IP](virtual-network-network-interface-addresses.md#change-ip-config) et [Supprimer une configuration IP](virtual-network-network-interface-addresses.md#delete-ip-config) de l’article [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md). Le transfert IP et l’affectation de sous-réseau sont également configurés dans cette section. Pour en savoir plus sur ces paramètres, lisez les sections [Activer ou désactiver le transfert IP](#ip-forwarding) et [Modifier l’affectation de sous-réseau](#subnet) de cet article.
    - **Serveurs DNS :** vous pouvez spécifier à quel serveur DNS une carte réseau est affectée par le biais des serveurs DHCP Azure. La carte réseau peut hériter du paramètre du réseau virtuel auquel elle est connectée, ou avoir un paramètre personnalisé qui remplace le paramètre pour le réseau virtuel auquel elle est connectée. Pour modifier ce qui est affiché, procédez de la manière décrite dans la section [Modifier les serveurs DNS](#dns) de cet article.
    - **Groupe de sécurité réseau (NSG) :** indique le groupe de sécurité réseau (éventuel) associé à la carte réseau. Un groupe de sécurité réseau contient des règles entrantes et sortantes pour filtrer le trafic réseau transitant par la carte réseau. Si un groupe de sécurité réseau est associé à la carte réseau, le nom du groupe de sécurité réseau associé s’affiche. Pour modifier ce qui est affiché, suivez les étapes de la section [Associer un groupe de sécurité réseau ou dissocier un groupe de sécurité réseau vis-à-vis d’une interface réseau](#associate-nsg) de cet article.
    - **Propriétés :** affiche des paramètres clés concernant la carte réseau, dont son adresse MAC (vide si la carte n’est pas attachée à une machine virtuelle), ainsi que l’abonnement dans lequel elle existe.
    - **Obtenir les règles de sécurité effectives :** les règles de sécurité sont répertoriées si la carte réseau est attachée à une machine virtuelle en cours d’exécution, tandis qu’un groupe de sécurité réseau est associé à la carte réseau et/ou au sous-réseau auquel il est connecté. Pour en savoir plus sur ce qui est affiché, listez l’article [Résoudre les problèmes relatifs aux groupes de sécurité réseau](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface). Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Groupes de sécurité réseau](virtual-networks-nsg.md).
    - **Obtenir les routages effectifs :** les routages sont répertoriés si la carte réseau est attachée à une machine virtuelle en cours d’exécution. Les itinéraires sont une combinaison d’itinéraires par défaut d’Azure, d’itinéraires définis par l’utilisateur et de tous les itinéraires BGP existants pour le sous-réseau auquel la carte réseau est connectée. Pour en savoir plus sur ce qui est affiché, consultez l’article [Résoudre les problèmes relatifs aux itinéraires](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Pour plus d’informations sur les itinéraires par défaut Azure et les itinéraires définis par l’utilisateur, voir [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).
    - **Paramètres courants d’Azure Resource Manager :** pour en savoir plus sur les paramètres courants d’Azure Resource Manager, consultez les articles [Journal d’activité](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Contrôle d’accès (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Balises](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Verrous](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Script Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) pour afficher les cartes réseau dans l’abonnement ; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) pour afficher les paramètres d’une carte réseau|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) pour afficher les cartes réseau dans l’abonnement ou pour afficher les paramètres d’une carte réseau|

## <a name="dns"></a>Modifier les serveurs DNS

Le serveur DNS est affecté par le serveur DHCP Azure à la carte réseau au sein du système d’exploitation de la machine virtuelle. Le serveur DNS affecté est tout serveur DNS paramétré pour une carte réseau. Pour en savoir plus sur les paramètres de résolution de noms d’une carte réseau, consultez l’article [Résolution de noms pour les machines virtuelles](virtual-networks-name-resolution-for-vms-and-role-instances.md). La carte réseau peut hériter les paramètres du réseau virtuel, ou utiliser ses propres paramètres uniques qui remplacent le paramètre pour le réseau virtuel.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur la carte réseau que vous souhaitez afficher ou dont vous souhaitez modifier les paramètres.
4. Dans le panneau de la carte réseau que vous avez sélectionnée, cliquez sur **Serveurs DNS** sous **PARAMÈTRES**.
5. Cliquez sur l’une des deux options suivantes :
    - **Hériter de VNet (par défaut)**: choisissez cette option afin d’hériter le paramètre de serveur DNS défini pour le réseau virtuel auquel la carte réseau est connectée. Au niveau du réseau virtuel, soit un serveur DNS personnalisé, soit le serveur DNS fourni par Azure est défini. Le serveur DNS fourni par Azure peut résoudre les noms d’hôtes pour les ressources connectées au même réseau virtuel. Le Nom de domaine complet (FQDN) doit être utilisé pour résoudre les ressources connectées à différents réseaux virtuels.
    - **Personnalisé** : vous pouvez configurer votre propre serveur DNS pour résoudre les noms sur plusieurs réseaux virtuels. Entrez l’adresse IP du serveur que vous souhaitez utiliser comme serveur DNS. L’adresse de serveur DNS que vous spécifiez est affectée uniquement à cette carte réseau et remplace tous les paramètres DNS du réseau virtuel auquel la carte réseau est connectée.
6. Cliquez sur **Save**.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-forwarding"></a>Activer ou désactiver le transfert IP

Le transfert IP permet à la machine virtuelle à laquelle une carte réseau est attachée de :
- Recevoir le trafic réseau qui n’est pas destiné à l’une des adresses IP affectées à l’une des configurations IP affectées à la carte réseau.
- Envoyer le trafic réseau avec une adresse IP source différente de celle affectée à l’une des configurations IP d’une carte réseau.

Ce paramètre doit être activé pour chaque carte réseau attachée à la machine virtuelle recevant le trafic que la machine virtuelle doit transférer. Une machine virtuelle peut transférer le trafic, qu’une ou plusieurs cartes réseau lui soient attachées. Bien que le transfert IP soit un paramètre Azure, la machine virtuelle doit également exécuter une application capable de transférer le trafic, comme une application de pare-feu, d’optimisation WAN et d’équilibrage de charge. Lorsqu’une machine virtuelle exécute des applications réseau, la machine virtuelle est communément appelée appliance virtuelle réseau (NVA). Vous pouvez afficher une liste de NVA prêtes au déploiement sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Le transfert IP est généralement utilisé avec les routages définis par l’utilisateur. Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez l’article [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Cartes réseau** qui s’affiche, cliquez sur la carte réseau pour laquelle vous voulez activer ou désactiver le transfert IP.
4. Dans le panneau relatif à la carte réseau que vous avez sélectionnée, cliquez sur **Configurations IP** dans la section **PARAMÈTRES**.
5. Cliquez sur **Activé** ou **Désactivé** (paramètre par défaut) pour modifier le paramètre.
6. Cliquez sur **Save**.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="subnet"></a>Modifier l’affectation de sous-réseau

Vous pouvez modifier le sous-réseau auquel une carte réseau est connectée, mais pas le réseau virtuel.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Dans le panneau **Interfaces réseau** qui s’affiche, cliquez sur la carte réseau que vous souhaitez afficher ou dont vous souhaitez modifier les paramètres.
4. Dans le panneau de la carte réseau que vous avez sélectionnée, sous **PARAMÈTRES**, cliquez sur **Configurations IP**. Si des adresses IP privées affectées à des configurations IP répertoriées sont assorties de la mention **(Statique)**, vous devez modifier la méthode d’affectation d’adresse IP en dynamique en procédant comme suit. Toutes les adresses IP privées doivent être affectées à l’aide de la méthode d’affectation dynamique pour modifier l’affectation de sous-réseau pour la carte réseau. Si les adresses sont affectées à l’aide de la méthode dynamique, passez à l’étape cinq. Si des adresses sont affectées à l’aide de la méthode d’attribution statique, procédez comme suit pour modifier la méthode d’affectation en dynamique :
    - Cliquez sur la configuration IP pour laquelle vous souhaitez modifier la méthode d’affectation d’adresse IP dans la liste des configurations IP.
    - Dans le panneau qui s’affiche pour la configuration IP, cliquez sur **Dynamique** pour la méthode **Affectation**.
    - Cliquez sur **Save**.
5. Sélectionnez le sous-réseau auquel vous souhaitez connecter la carte réseau à partir de la liste déroulante **Sous-réseau**.
6. Cliquez sur **Save**. De nouvelles adresses dynamiques sont affectées à partir de la plage d’adresses de sous-réseau pour le nouveau sous-réseau. Après avoir affecté la carte réseau à un nouveau sous-réseau, vous pouvez affecter une adresse IP statique à partir de la nouvelle plage d’adresses de sous-réseau si vous le souhaitez. Pour en savoir plus sur l’ajout, la modification et la suppression d’adresses IP pour une carte réseau, voir [Ajouter, modifier ou supprimer des adresses IP](virtual-network-network-interface-addresses.md).

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-nic"></a>Supprimer une carte réseau

Vous pouvez supprimer une carte réseau tant qu’elle n’est pas attachée à une machine virtuelle. Si elle est connectée à une machine virtuelle, vous devez mettre celle-ci à l’état d’arrêt (désallouée), puis détacher la carte réseau de la machine virtuelle avant de pouvoir la supprimer. Pour détacher une carte réseau d’une machine virtuelle, procédez de la manière décrite dans la section [Détacher une carte réseau d’une machine virtuelle existante](virtual-network-network-interface-vm.md#vm-remove-nic) de l’article [Ajouter ou supprimer des interfaces réseau](virtual-network-network-interface-vm.md). La suppression d’une machine virtuelle a pour effet de détacher toutes les cartes réseau qui y sont attachées, mais pas de les supprimer.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com) à l’aide d’un compte disposant (au minimum) des autorisations associées au rôle Collaborateur de réseau de votre abonnement. Consultez l’article [Rôles intégrés pour contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) afin d’en savoir plus sur l’affectation des rôles et des autorisations aux comptes.
2. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, saisissez *interfaces réseau*. Lorsque la mention **interfaces réseau** apparaît dans les résultats de recherche, cliquez dessus.
3. Cliquez avec le bouton droit sur la carte réseau à supprimer, puis cliquez sur **Supprimer**.
4. Cliquez sur **Oui** pour confirmer la suppression de la carte réseau.

Lorsque vous supprimez une carte réseau, toutes les adresses MAC ou IP qui lui sont affectées sont libérées.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Étapes suivantes
Pour créer une machine virtuelle avec plusieurs cartes réseau ou adresses IP, lisez les articles suivants :

**Commandes**

|Tâche|Outil|
|---|---|
|Créer une machine virtuelle avec plusieurs cartes d’interface réseau|[INTERFACE DE LIGNE DE COMMANDE](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Créer une machine virtuelle à carte réseau unique avec plusieurs adresses IP|[INTERFACE DE LIGNE DE COMMANDE](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

