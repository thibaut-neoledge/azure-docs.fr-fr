---
title: "Créer un réseau virtuel Azure comprenant plusieurs sous-réseaux | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel comprenant des sous-réseaux dans Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f82a95ec9543b2d53ef28bf7f15315e23cf4893a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Créer un réseau virtuel comprenant plusieurs sous-réseaux

Ce didacticiel explique comment créer un réseau virtuel Azure de base comprenant des sous-réseaux publics et privés distincts. Les ressources présentes dans les réseaux virtuels peuvent communiquer entre elles et avec les ressources d’autres réseaux connectés à un réseau virtuel. Vous pouvez créer des ressources Azure, telles que des machines virtuelles, des environnements App Service Environment, des groupes de machines virtuelles identiques, Azure HDInsight et des services cloud dans des sous-réseaux identiques ou différents au sein d’un réseau virtuel. Créer des ressources dans des sous-réseaux différents vous permet de filtrer le trafic entrant et sortant des sous-réseaux indépendamment avec des [groupes de sécurité réseau](virtual-networks-create-nsg-arm-pportal.md) et [d’acheminer le trafic entre les sous-réseaux](virtual-network-create-udr-arm-ps.md) via des appliances virtuelles réseau, telles qu’un pare-feu, si vous le souhaitez. 

Les sections suivantes incluent des mesures que vous pouvez prendre pour créer un réseau virtuel à l’aide du [portail Azure](#portal), de l’interface de ligne de commande Azure ([Azure CLI](#azure-cli)), d’[Azure PowerShell](#powershell) et d’un [modèle Azure Resource Manager](#resource-manager-template). Le résultat est le même, quel que soit l’outil choisi pour créer le réseau virtuel. Cliquez sur un lien d’outil pour accéder à la section correspondante du didacticiel. En savoir plus sur tous les paramètres des [réseaux virtuels](virtual-network-manage-network.md) et des [sous-réseaux](virtual-network-manage-subnet.md).

Cet article explique comment créer un réseau virtuel par le biais du modèle de déploiement Resource Manager, qui est le modèle de déploiement que nous recommandons lors de la création de réseaux virtuels. Pour créer un réseau virtuel (classique), consultez [Créer un réseau virtuel (classique)](create-virtual-network-classic.md). Si vous ne connaissez pas les modèles de déploiement Azure, consultez [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="portal"></a>Portail Azure

1. Dans un navigateur Internet, accédez au [portail Azure](https://portal.azure.com). Connectez-vous à votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Dans le portail, cliquez sur **+Nouveau** > **Mise en réseau** > **Réseau virtuel**.
3. Dans le panneau **Créer un réseau virtuel**, entrez les valeurs suivantes, puis cliquez sur **Créer**:

    |Paramètre|Valeur|
    |---|---|
    |Nom|myVnet|
    |Espace d’adressage|10.0.0.0/16|
    |Nom du sous-réseau|Public|
    |Plage d’adresses de sous-réseau|10.0.0.0/24|
    |Groupe de ressources|Laissez l’option **Créer** activée, puis entrez **myResourceGroup**.|
    |Abonnement et emplacement|Sélectionnez votre abonnement et son emplacement.

    Si vous débutez avec Azure, apprenez-en davantage sur les [groupes de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), les [abonnements](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) et les [emplacements](https://azure.microsoft.com/regions) (également appelés *régions*).
4. Dans le portail, lorsque vous créez un réseau virtuel, vous ne pouvez créer qu’un seul sous-réseau. Dans ce didacticiel, vous créez un deuxième sous-réseau après avoir créé le réseau virtuel. Vous pouvez créer ultérieurement des ressources accessibles sur Internet dans le sous-réseau **Public**. Vous pouvez également créer des ressources non accessibles à partir d’Internet dans le sous-réseau **Privé**. Pour créer le deuxième sous-réseau, dans la zone **Rechercher des ressources** en haut de la page, entrez **myVnet**. Dans les résultats de recherche, cliquez sur **myVnet**. Si vous avez plusieurs réseaux virtuels du même nom dans votre abonnement, vérifiez les groupes de ressources répertoriés sous chaque réseau virtuel. Veillez à cliquer sur le résultat de recherche **myVnet** dont le groupe de ressources est **myResourceGroup**.
5. Dans le panneau **myVnet**, sous **Paramètres**, cliquez sur **Sous-réseaux**.
6. Dans le panneau **myVnet - Sous-réseaux**, cliquez sur **+Sous-réseau**.
7. Dans le panneau **Ajouter un sous-réseau**, pour **Nom**, entrez **Privé**. Pour **Plage d’adresses**, entrez **10.0.1.0/24**.  Cliquez sur **OK**.
8. Dans le panneau **myVnet - Sous-réseaux**, passez en revue les sous-réseaux. Vous voyez les sous-réseaux **Publics** et **Privés** que vous avez créés.
9. **Facultatif :** effectuez les autres didacticiels répertoriés dans la section [Étapes suivantes](#next-steps) pour filtrer le trafic entrant et sortant de chaque sous-réseau avec des groupes de sécurité réseau, pour acheminer le trafic entre les sous-réseaux via une appliance virtuelle réseau ou pour connecter le réseau virtuel à d’autres réseaux virtuels ou à des réseaux locaux.
10. **Facultatif :** supprimez les ressources que vous avez créées dans ce didacticiel en suivant les étapes indiquées dans la section [Supprimer des ressources](#delete-portal).

## <a name="azure-cli"></a>Interface de ligne de commande Azure

Les commandes d’Azure CLI sont identiques, que vous les exécutiez à partir de Windows, de Linux ou de macOS. Toutefois, il existe des différences de script entre les interpréteurs de commandes du système d’exploitation. Le script présenté dans les étapes suivantes s’exécute dans un interpréteur de commandes Bash. 

1. [Installez et configurez Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`. Au lieu d’installer CLI et ses prérequis, vous pouvez utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée sur Cloud Shell pour être utilisée avec votre compte. Pour utiliser Cloud Shell, cliquez sur le bouton Cloud Shell (**>_**) en haut du [portail](https://portal.azure.com) ou cliquez simplement sur le bouton *Essayer* dans les étapes qui suivent. 
2. Si l’interface CLI s’exécute localement, connectez-vous à Azure avec la commande `az login`. Si vous utilisez Cloud Shell, cela signifie que vous êtes déjà connecté.
3. Passez en revue le script suivant et ses commentaires. Dans votre navigateur, copiez le script et collez-le dans votre session CLI :

    ```azurecli-interactive
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name myResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet named Public.
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet named Private in the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name myVnet \
      --resource-group myResourceGroup
    ```
    
4. Une fois l’exécution du script terminée, passez en revue les sous-réseaux du réseau virtuel. Copiez la commande suivante et collez-la dans votre session CLI :

    ```azurecli
    az network vnet subnet list --resource-group myResourceGroup --vnet-name myVnet --output table
    ```

5. **Facultatif :** effectuez les autres didacticiels répertoriés dans la section [Étapes suivantes](#next-steps) pour filtrer le trafic entrant et sortant de chaque sous-réseau avec des groupes de sécurité réseau, pour acheminer le trafic entre les sous-réseaux via une appliance virtuelle réseau ou pour connecter le réseau virtuel à d’autres réseaux virtuels ou à des réseaux locaux.
6. **Facultatif :** supprimez les ressources que vous avez créées dans ce didacticiel en suivant les étapes indiquées dans la section [Supprimer des ressources](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Installez la dernière version du module PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Dans une session PowerShell, connectez-vous à Azure avec votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) à l’aide de la commande `login-azurermaccount`.

3. Passez en revue le script suivant et ses commentaires. Dans votre navigateur, copiez le script et collez-le dans votre session PowerShell :

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location eastus
    
    # Create the public and private subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Location eastus `
      -Name myVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    ```

4. Pour passer en revue les sous-réseaux du réseau virtuel, copiez la commande suivante, puis collez-la dans votre session PowerShell :

    ```powershell
    $Vnet.subnets | Format-Table Name, AddressPrefix
    ```

5. **Facultatif :** effectuez les autres didacticiels répertoriés dans la section [Étapes suivantes](#next-steps) pour filtrer le trafic entrant et sortant de chaque sous-réseau avec des groupes de sécurité réseau, pour acheminer le trafic entre les sous-réseaux via une appliance virtuelle réseau ou pour connecter le réseau virtuel à d’autres réseaux virtuels ou à des réseaux locaux.
6. **Facultatif :** supprimez les ressources que vous avez créées dans ce didacticiel en suivant les étapes indiquées dans la section [Supprimer des ressources](#delete-powershell).

## <a name="resource-manager-template"></a>Modèle Resource Manager

Vous pouvez déployer un groupe de machines virtuelles à l’aide d’un modèle Azure Resource Manager. Pour en savoir plus sur les modèles, voir [Qu’est-ce que Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Pour accéder au modèle et découvrir ses paramètres, voir le modèle [Créer un réseau virtuel avec deux sous-réseaux](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Vous pouvez déployer le modèle à l’aide du [portail](#template-portal), d’[Azure CLI](#template-cli) ou de [PowerShell](#template-powershell).

Étapes facultatives après avoir déployé le modèle :

1. Effectuez les autres didacticiels répertoriés dans la section [Étapes suivantes](#next-steps) pour filtrer le trafic entrant et sortant de chaque sous-réseau avec des groupes de sécurité réseau, pour acheminer le trafic entre les sous-réseaux via une appliance virtuelle réseau ou pour connecter le réseau virtuel à d’autres réseaux virtuels ou à des réseaux locaux.
2. Supprimez les ressources que vous avez créées dans ce didacticiel en suivant les étapes indiquées dans la section [Supprimer des ressources](#delete).

### <a name="template-portal"></a>Portail Azure

1. Dans votre navigateur, ouvrez la [page du modèle](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Cliquez sur le bouton **Déployer dans Azure**. Si vous n’êtes pas déjà connecté à Azure, connectez-vous sur l’écran de connexion du portail Azure qui s’affiche.
3. Connectez-vous au portail Azure à l’aide de votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Entrez les valeurs suivantes pour les paramètres :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Sélectionnez votre abonnement|
    |Groupe de ressources|myResourceGroup|
    |Emplacement|Sélectionner un emplacement|
    |Nom du réseau virtuel|myVnet|
    |Préfixe d’adresse de réseau virtuel|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Public|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privé|

5. Acceptez les conditions générales, puis cliquez sur **Acheter** pour déployer le réseau virtuel.

### <a name="template-cli"></a>Interface CLI Azure

1. [Installez et configurez Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Assurez-vous que la version la plus récente d’Azure CLI est installée. Pour obtenir de l’aide sur les commandes CLI, entrez `az <command> --help`. Au lieu d’installer CLI et ses prérequis, vous pouvez utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée sur Cloud Shell pour être utilisée avec votre compte. Pour utiliser Cloud Shell, cliquez sur le bouton Cloud Shell **>_** en haut du [portail](https://portal.azure.com) ou cliquez simplement sur le bouton **Essayer** dans les étapes qui suivent. 
2. Si l’interface CLI s’exécute localement, connectez-vous à Azure avec la commande `az login`. Si vous utilisez Cloud Shell, cela signifie que vous êtes déjà connecté.
3. Pour créer un groupe de ressources destiné au réseau virtuel, copiez la commande suivante et collez-la dans votre session CLI :

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    ```
    
4. Vous pouvez déployer le modèle en utilisant l’une des options de paramètres suivantes :
    - **Valeurs de paramètres par défaut**. Entrez la commande suivante :
    
        ```azurecli-interactive
        az group deployment create --resource-group myResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
        ```
    - **Valeurs de paramètres personnalisées**. Téléchargez et modifiez le modèle avant de le déployer. Vous pouvez également déployer le modèle à l’aide de paramètres dans la ligne de commande, ou d’un fichier de paramètres distinct. Pour télécharger les fichiers de modèle et de paramètres, cliquez sur le bouton **Parcourir sur GitHub** dans la page de modèle [Créer un réseau virtuel avec deux sous-réseaux](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Dans GitHub, cliquez sur le fichier **azuredeploy.parameters.json** ou **azuredeploy.json**. Ensuite, cliquez sur le bouton **Brut** pour afficher le fichier. Dans votre navigateur, copiez le contenu du fichier. Enregistrez le contenu dans un fichier sur votre ordinateur. Vous pouvez modifier les valeurs des paramètres dans le modèle, ou déployer le modèle avec un fichier de paramètres séparé.  

    Pour en savoir plus sur le déploiement de modèles à l’aide de ces méthodes, tapez `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Installez la dernière version du module PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Dans une session PowerShell, pour vous connecter avec votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), entrez `login-azurermaccount`.
3. Pour créer un groupe de ressources destiné au réseau virtuel, entrez la commande suivante :

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location eastus
    ```
    
4. Vous pouvez déployer le modèle en utilisant l’une des options de paramètres suivantes :
    - **Valeurs de paramètres par défaut**. Entrez la commande suivante :
    
        ```powershell
        New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName myResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json
        ```
        
    - **Valeurs de paramètres personnalisées**. Téléchargez et modifiez le modèle avant de le déployer. Vous pouvez également déployer le modèle à l’aide de paramètres dans la ligne de commande, ou d’un fichier de paramètres distinct. Pour télécharger les fichiers de modèle et de paramètres, cliquez sur le bouton **Parcourir sur GitHub** dans la page de modèle [Créer un réseau virtuel avec deux sous-réseaux](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Dans GitHub, cliquez sur le fichier **azuredeploy.parameters.json** ou **azuredeploy.json**. Ensuite, cliquez sur le bouton **Brut** pour afficher le fichier. Dans votre navigateur, copiez le contenu du fichier. Enregistrez le contenu dans un fichier sur votre ordinateur. Vous pouvez modifier les valeurs des paramètres dans le modèle, ou déployer le modèle avec un fichier de paramètres séparé.  

    Pour en savoir plus sur le déploiement de modèles à l’aide de ces méthodes, tapez `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Supprimer des ressources

Lorsque vous aurez terminé ce didacticiel, vous souhaiterez peut-être supprimer les ressources créées afin que leur utilisation ne soit pas facturée. La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

### <a name="delete-portal"></a>Portail Azure

1. Dans le champ de recherche du portail, entrez **myResourceGroup**. Dans les résultats de la recherche, cliquez sur **myResourceGroup**.
2. Dans le panneau **myResourceGroup**, cliquez sur l’icône **Supprimer**.
3. Pour confirmer la suppression, dans le champ **TAPEZ LE NOM DU GROUPE DE RESSOURCES :**, entrez **myResourceGroup**, puis cliquez sur **Supprimer**.

### <a name="delete-cli"></a>Interface CLI Azure

Dans une session CLI, entrez la commande suivante :

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Dans une session PowerShell, entrez la commande suivante :

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir tous les paramètres de réseau virtuel et de sous-réseau, consultez [Gérer des réseaux virtuels](virtual-network-manage-network.md#view-vnet) et [Gérer les sous-réseaux virtuels](virtual-network-manage-subnet.md#create-subnet). Vous disposez de différentes options pour l’utilisation de réseaux virtuels et de sous-réseaux dans un environnement de production afin de répondre à différentes exigences.
- Filtrez le trafic de sous-réseau entrant et sortant en créant et en appliquant des [groupes de sécurité réseau](virtual-networks-nsg.md) aux sous-réseaux.
- Acheminez le trafic entre les sous-réseaux via une appliance virtuelle réseau, en créant des [itinéraires définis par l’utilisateur](virtual-network-create-udr-arm-ps.md), et appliquez les itinéraires à chaque sous-réseau.
- Créez une machine virtuelle [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dans un réseau virtuel existant.
- Connectez deux réseaux virtuels en créant un [appairage de réseau virtuel](virtual-network-peering-overview.md) entre les réseaux virtuels.
- Connectez le réseau virtuel à un réseau local en utilisant un circuit [Passerelle VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
