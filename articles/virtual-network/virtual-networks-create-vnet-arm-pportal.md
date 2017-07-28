---
title: "Créer un réseau virtuel Azure comprenant des sous-réseaux | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel comprenant des sous-réseaux dans Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: jdial
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1ecfba0470c31d1119b5e6c0f9d8538abe40f834
ms.contentlocale: fr-fr
ms.lasthandoff: 06/01/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Créer un réseau virtuel comprenant plusieurs sous-réseaux

Ce didacticiel explique comment créer un réseau virtuel Azure de base comprenant des sous-réseaux publics et privés distincts. Vous pouvez connecter des ressources Azure, telles que des machines virtuelles, des environnements App Service, des groupes de machines virtuelles identiques, Azure HDInsight et d’autres services cloud aux sous-réseaux. Les ressources connectées aux réseaux virtuels peuvent communiquer entre elles sur votre réseau Azure privé.

Les sections suivantes incluent des mesures que vous pouvez prendre pour déployer un réseau virtuel à l’aide du [portail Azure](#portal), de l’interface de ligne de commande Azure ([Azure CLI](#azure-cli)), d’[Azure PowerShell](#powershell) et d’un [modèle Azure Resource Manager](#resource-manager-template). Le résultat est le même, quel que soit l’outil choisi pour déployer le réseau virtuel. Cliquez sur un lien d’outil pour accéder à la section correspondante du didacticiel. Pour plus d’informations sur tous les paramètres de réseau virtuel et de sous-réseau, voir [Gérer les réseaux virtuels](virtual-network-manage-network.md) et [Gérer les sous-réseaux virtuels](virtual-network-manage-subnet.md).

## <a name="portal"></a>Portail Azure

1. Dans un navigateur Internet, accédez au [portail Azure](https://portal.azure.com). Connectez-vous à l’aide de votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Dans le portail, cliquez sur **+Nouveau** > **Mise en réseau** > **Réseau virtuel**.
3. Dans le panneau **Réseau virtuel**, sous **Sélectionnez un modèle de déploiement**, laissez l’option **Gestionnaire des ressources** activée, puis cliquez sur **Créer**.
4. Dans le panneau **Créer un réseau virtuel**, entrez les valeurs suivantes, puis cliquez sur **Créer**:

    |Paramètre|Valeur|
    |---|---|
    |Nom|MyVnet|
    |Espace d’adressage|10.0.0.0/16|
    |Nom du sous-réseau|Public|
    |Plage d’adresses de sous-réseau|10.0.0.0/24|
    |Groupe de ressources|Laissez l’option **Créer un nouveau** activée, puis entrez **MyResourceGroup**.|
    |Abonnement et emplacement|Sélectionnez votre abonnement et son emplacement.

    Si vous débutez avec Azure, apprenez-en davantage sur les [groupes de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), les [abonnements](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) et les [emplacements](https://azure.microsoft.com/regions) (également appelés *régions*).
6. Dans le portail, lorsque vous créez un réseau virtuel, vous ne pouvez créer qu’un seul sous-réseau. Dans ce didacticiel, vous créez un deuxième sous-réseau après avoir créé le réseau virtuel. Vous pouvez connecter ultérieurement des ressources accessibles sur Internet à au sous-réseau **Public**. Vous pouvez également connecter des ressources non accessibles à partir d’Internet à un sous-réseau **Privé**. Pour créer le deuxième sous-réseau, dans la zone **Rechercher des ressources** en haut de la page, entrez **MyVnet**. Dans les résultats de recherche, cliquez sur **MyVnet**. Si vous avez plusieurs réseaux virtuels du même nom dans votre abonnement, vérifiez les groupes de ressources répertoriés sous chaque réseau virtuel. Veillez à cliquer sur le résultat de recherche **MyVnet** dont le groupe de ressources est **MyResourceGroup**.
7. Dans le panneau **MyVnet**, sous **Paramètres**, cliquez sur **Sous-réseaux**.
8. Dans le panneau **MyVnet - Sous-réseaux**, cliquez sur **+Sous-réseau**.
9. Dans le panneau **Ajouter un sous-réseau**, pour **Nom**, entrez **Privé**. Pour **Plage d’adresses**, entrez **10.0.1.0/24**.  Cliquez sur **OK**.
10. Dans le panneau **MyVnet - Sous-réseaux**, passez en revue les sous-réseaux. Vous voyez les sous-réseaux **Publics** et **Privés** que vous avez créés.
11. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-portal) de cet article.

## <a name="azure-cli"></a>Interface de ligne de commande Azure

Les commandes d’Azure CLI sont identiques, que vous les exécutiez à partir de Windows, de Linux ou de macOS. Toutefois, il existe des différences de script entre les interpréteurs de commandes du système d’exploitation. Les instructions ci-après ont trait à l’exécution d’un script bash contenant des commandes d’Azure CLI :

1. Dans un navigateur Internet, ouvrez le [portail Azure](https://portal.azure.com). Connectez-vous à l’aide de votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En haut du portail, à droite de la zone **Rechercher des ressources**, cliquez sur l’icône **>_** pour démarrer Bash Azure Cloud Shell (préversion). Le volet Cloud Shell apparaît au bas du portail. Après quelques secondes, une invite **username@Azure:~$** s’affiche. Cloud Shell vous connecte automatiquement à Azure en utilisant les informations d’identification à l’aide desquelles vous vous êtes authentifié auprès du portail.
3. Dans votre navigateur, copiez le script suivant :
    ```azurecli
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name MyResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet.
    az network vnet create \
      --name MyVnet \
      --resource-group MyResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet within the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Créez un fichier de script et enregistrer-le. À l’invite de commandes de Cloud Shell, tapez `nano myscript.sh --nonewlines`. La commande démarre l’éditeur nano GNU avec un fichier vide myscript.sh vide. Positionnez la souris à l’intérieur de la fenêtre de l’éditeur, cliquez avec le bouton droit, puis cliquez sur **Coller**.  
5. Pour enregistrer le fichier sous myscript.sh, appuyez sur Ctrl+X, tapez **Y**, puis appuyez sur la touche Entrée. Le stockage de Cloud Shell ne conserve pas les fichiers enregistrés entre les sessions. Si vous souhaitez conserver le script entre les sessions Cloud Shell, configurez un [stockage persistant](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour Cloud Shell, et enregistrez le fichier dans le stockage persistant.
6. À l’invite de commandes Cloud Shell, pour marquer le fichier comme exécutable, exécutez la commande `chmod +x myscript.sh`.
7. Pour exécuter le script, entrez `./myscript.sh`.
8. Une fois l’exécution du script terminée, passez en revue les sous-réseaux du réseau virtuel, copiez la commande suivante, puis collez-la dans le volet Bash Cloud Shell :
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-cli) de cet article.

## <a name="powershell"></a>PowerShell

1. Installez la dernière version du module PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Pour démarrer une session PowerShell, accédez à **Démarrer**, entrez **powershell**, puis cliquez sur **PowerShell**.
3. Dans la fenêtre PowerShell, pour vous connecter avec votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), entrez `login-azurermaccount`.
4. Dans votre navigateur, copiez le script suivant :
    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Pour exécuter le script, cliquez avec le bouton droit dans la fenêtre PowerShell.
6. Pour passer en revue les sous-réseaux du réseau virtuel, copiez la commande suivante, puis collez-la dans la fenêtre PowerShell :
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-powershell) de cet article.

## <a name="resource-manager-template"></a>Modèle Resource Manager

Vous pouvez déployer un groupe de machines virtuelles à l’aide d’un modèle Azure Resource Manager. Pour en savoir plus sur les modèles, voir [Qu’est-ce que Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Pour accéder au modèle et découvrir ses paramètres, voir le modèle [Créer un réseau virtuel avec deux sous-réseaux](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Vous pouvez déployer le modèle à l’aide du [portail](#template-portal), d’[Azure CLI](#template-cli) ou de [PowerShell](#template-powershell).

**Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans toute sous-section de la section [Supprimer des ressources](#delete) de cet article.

### <a name="template-portal"></a>Portail Azure

1. Dans votre navigateur, ouvrez la [page du modèle](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Cliquez sur le bouton **Déployer dans Azure**. Cela a pour effet d’ouvrir la page de connexion au portail Azure.
3. Connectez-vous au portail Azure à l’aide de votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Entrez les valeurs suivantes pour les paramètres :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Sélectionnez votre abonnement|
    |Groupe de ressources|myResourceGroup|
    |Lieu|Sélectionner un emplacement|
    |Nom du réseau virtuel|MyVnet|
    |Préfixe d’adresse de réseau virtuel|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Public|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privé|

5. Acceptez les conditions générales, puis cliquez sur **Acheter** pour déployer le réseau virtuel.

### <a name="template-cli"></a>Interface CLI Azure

1. Dans le [portail](https://portal.azure.com), connectez-vous avec votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. À droite de la zone **Rechercher des ressources**, cliquez sur l’icône **> _** pour démarrer Bash Azure Cloud Shell (préversion). Le volet Cloud Shell apparaît au bas du portail. Après quelques secondes, une invite **username@Azure:~$** s’affiche. Cloud Shell vous connecte automatiquement à Azure en utilisant les informations d’identification que vous avez utilisées pour vous connecter au portail Azure.
3. Pour créer un groupe de ressources pour le réseau virtuel, entrez la commande suivante : `az group create --name MyResourceGroup --location eastus`
4. Vous pouvez déployer le modèle en utilisant l’une des options de paramètres suivantes :
    - **Valeurs de paramètres par défaut**. Entrez la commande suivante :   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **Valeurs de paramètres personnalisées**. Téléchargez et modifiez le modèle avant de le déployer. Vous pouvez également déployer le modèle à l’aide de paramètres dans la ligne de commande, ou d’un fichier de paramètres distinct. Pour télécharger les fichiers de modèle et de paramètres, cliquez sur le bouton **Parcourir sur GitHub** dans la page de modèle [Créer un réseau virtuel avec deux sous-réseaux](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Dans GitHub, cliquez sur le fichier **azuredeploy.parameters.json** ou **azuredeploy.json**. Ensuite, cliquez sur le bouton **Brut** pour afficher le fichier. Dans votre navigateur, copiez le contenu du fichier. Enregistrez le contenu dans un fichier sur votre ordinateur. Vous pouvez modifier les valeurs des paramètres dans le modèle, ou déployer le modèle avec un fichier de paramètres séparé.  

    Pour en savoir plus sur le déploiement de modèles à l’aide de ces méthodes, tapez `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Installez la dernière version du module PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Pour démarrer une session PowerShell, accédez à **Démarrer**, entrez **powershell**, puis cliquez sur **PowerShell**.
3. Dans la fenêtre PowerShell, pour vous connecter avec votre [compte Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), entrez `login-azurermaccount`.
4. Pour créer un groupe de ressources pour le réseau virtuel, entrez la commande suivante : `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. Vous pouvez déployer le modèle en utilisant l’une des options de paramètres suivantes :
    - **Valeurs de paramètres par défaut**. Entrez la commande suivante :   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **Valeurs de paramètres personnalisées**. Téléchargez et modifiez le modèle avant de le déployer. Vous pouvez également déployer le modèle à l’aide de paramètres dans la ligne de commande, ou d’un fichier de paramètres distinct. Pour télécharger les fichiers de modèle et de paramètres, cliquez sur le bouton **Parcourir sur GitHub** dans la page de modèle [Créer un réseau virtuel avec deux sous-réseaux](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Dans GitHub, cliquez sur le fichier **azuredeploy.parameters.json** ou **azuredeploy.json**. Ensuite, cliquez sur le bouton **Brut** pour afficher le fichier. Dans votre navigateur, copiez le contenu du fichier. Enregistrez le contenu dans un fichier sur votre ordinateur. Vous pouvez modifier les valeurs des paramètres dans le modèle, ou déployer le modèle avec un fichier de paramètres séparé.  

    Pour en savoir plus sur le déploiement de modèles à l’aide de ces méthodes, tapez `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Supprimer des ressources
À l’issue de ce didacticiel, vous souhaiterez peut-être supprimer les ressources utilisées pour celui-ci afin d’éviter des frais d’utilisation. La suppression d’un groupe de ressources a également pour effet de supprimer toutes les ressources qu’il contient.

### <a name="delete-portal"></a>Portail Azure

1. Dans le champ de recherche du portail, entrez **MyResourceGroup**. Dans les résultats de la recherche, cliquez sur **MyResourceGroup**.
2. Dans le panneau **MyResourceGroup**, cliquez sur l’icône **Supprimer**.
3. Pour confirmer la suppression, dans le champ **Tapez le nom du groupe de ressources**, entrez **MyResourceGroup**, puis cliquez sur **Supprimer**.

### <a name="delete-cli"></a>Interface CLI Azure

À l’invite de commandes Cloud Shell, entrez la commande suivante : `az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

À l’invite de commandes PowerShell, entrez la commande suivante : `Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir tous les paramètres de réseau virtuel et de sous-réseau, voir [Gérer les réseaux virtuels](virtual-network-manage-network.md#view-vnet) et [Gérer les sous-réseaux virtuels](virtual-network-manage-subnet.md#create-subnet). Vous disposez de différentes options pour l’utilisation de réseaux virtuels et de sous-réseaux dans un environnement de production afin de répondre à différentes exigences.
- Créez des [groupes de sécurité réseau](virtual-networks-nsg.md) (NSG), et appliquez-les à des sous-réseaux afin de filtrer le trafic de sous-réseau entrant et sortant.
- Créez une [machine virtuelle Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou une [machine virtuelle Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), puis connectez-la au réseau virtuel.
- Connectez le réseau virtuel à un autre réseau virtuel dans le même emplacement en utilisant une [homologation de réseaux virtuels](virtual-network-peering-overview.md).
- Connectez le réseau virtuel à un réseau local en utilisant un [réseau virtuel privé de site à site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou un circuit [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

