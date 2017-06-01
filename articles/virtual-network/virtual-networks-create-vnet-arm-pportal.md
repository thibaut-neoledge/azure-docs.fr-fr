---
title: "Créer un réseau virtuel Azure | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel comprenant plusieurs sous-réseaux."
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 19857ad1e970ad32359708ded320c53a4778ef8c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Créer un réseau virtuel comprenant plusieurs sous-réseaux

Ce didacticiel explique comment créer un réseau virtuel Azure de base comprenant des sous-réseaux publics et privés distincts. Vous pouvez connecter des ressources Azure, telles que des machines virtuelles, des environnements App Service, des groupes de machines virtuelles identiques, HDInsight et des services cloud aux sous-réseaux. Les ressources connectées à des réseaux virtuels peuvent communiquer entre eux sur le réseau privé Azure.

Les sections suivantes décrivent les étapes de déploiement d’un réseau virtuel à l’aide du [portail Azure](#portal), d’[Azure CLI](#cli), d’[Azure PowerShell](#powershell) et d’un [modèle Azure Resource Manager](#template). Le résultat est le même, quel que soit l’outil choisi pour déployer le réseau virtuel. Un clic sur le lien d’un outil vous amène directement à la section correspondant dans l’article. Pour en savoir plus sur tous les paramètres de réseau virtuel et de sous-réseau, lisez les article [Gérer des réseaux virtuels](virtual-network-manage-network.md) et [Gérer des sous-réseaux](virtual-network-manage-subnet.md).

## <a name="portal"></a>Portail Azure

1. Dans un navigateur Internet, ouvrez le [portail](https://portal.azure.com) Azure et connectez-vous avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Dans le portail, cliquez sur **+ Nouveau** > **Mise en réseau** > **Réseau virtuel**.
3. Dans le panneau **Réseau virtuel** qui s’affiche, laissez l’option *Resource Manager* activée dans la boîte de dialogue **Sélectionner un modèle de déploiement**, puis cliquez sur **Créer**.
4. Dans le panneau **Créer un réseau virtuel** qui s’affiche, entrez les valeurs suivantes, puis cliquez sur le bouton **Créer**:

    |Paramètre|Valeur|
    |---|---|
    |Nom|*MyVnet*|
    |Espace d’adressage|*10.0.0.0/16*|
    |Nom du sous-réseau|Public|
    |Plage d’adresses de sous-réseau|*10.0.0.0/24*|
    |Groupe de ressources|Laissez l’option **Créer un nouveau** activée et entrez *MyResourceGroup*|
    |Abonnement et emplacement|Sélectionnez votre abonnement et son emplacement.

    Si vous débutez avec Azure, apprenez-en davantage sur les [groupes de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), les [abonnements](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) et les [emplacements](https://azure.microsoft.com/regions) (également appelés régions).
6. Le portail vous permet seulement de créer un sous-réseau lors de la création d’un réseau virtuel. Pour ce didacticiel, un deuxième sous-réseau est créé après le réseau virtuel. Vous pourrez connecter ultérieurement des ressources accessibles sur Internet au *Sous-réseau public*, et des ressources non accessibles sur Internet à un sous-réseau privé. Pour créer le deuxième sous-réseau, dans la zone *Rechercher des ressources* en haut du portail, entrez *MyVnet*. Lorsque **MyVnet** apparaît dans les résultats de recherche, cliquez dessus. Si votre abonnement comprend plusieurs réseaux virtuels du même nom, vous voyez les noms des groupes de ressources répertoriés sous chaque réseau virtuel du même nom. Veillez à cliquer sur le résultat MyVnet sous lequel figure *MyResourceGroup*.
7. Dans le panneau **MyVnet** qui s’affiche, sous **PARAMÈTRES**, cliquez sur **Sous-réseaux**.
8. Dans le panneau **MyVnet - Sous-réseaux**, cliquez sur **+Sous-réseau**.
9. Dans le panneau **Ajouter un sous-réseau**, entrez *Privé* pour le **Nom**, *10.0.1.0/24* pour la **Plage d’adresses**, puis cliquez sur **OK**.
10. Passez en revue les sous-réseaux dans le panneau **MyVnet - Sous-réseaux**. Vous voyez les sous-réseaux **Public** et **Privé**que vous avez créés.
11. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-portal) de cet article.

## <a name="cli"></a>Interface de ligne de commande
Bien que les commandes de l’interface de ligne de commande soient identiques que vous les exécutiez dans Windows, Linux ou macOS, il existe des différences de scripts entre les shells des systèmes d’exploitation. Les instructions ci-après ont trait à l’exécution d’un script bash contenant des commandes d’interface de ligne de commande :

1. Dans un navigateur Internet, ouvrez le [portail](https://portal.azure.com) Azure et connectez-vous avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En haut du portail, à droite de la barre *Rechercher des ressources*, cliquez sur l’icône **>_** pour démarrer un bash Azure Cloud Shell (version préliminaire). Le volet Cloud Shell s’affiche en bas du portail et, après quelques secondes, affiche une invite **username@Azure:~$**. Le Cloud Shell vous connecte automatiquement à Azure en utilisant les informations d’identification à l’aide desquelles vous vous êtes authentifié auprès du portail.
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
    
    # Create an additional subnet within the VNet.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Créez un fichier de script et enregistrer-le. À l’invite du Cloud Shell, tapez `nano myscript.sh --nonewlines`. La commande démarre l’éditeur nano GNU avec un fichier vide myscript.sh vide. Positionnez la souris à l’intérieur de la fenêtre de l’éditeur, cliquez avec le bouton droit, puis cliquez sur **Coller**. Le stockage du Cloud Shell n’est pas conservé entre les sessions. Si vous souhaitez conserver le script entre les sessions de Cloud Shell, configurez un [stockage persistant](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour le Cloud Shell. 
5. Sur votre clavier, maintenez enfoncées les touches **Ctrl+X**, tapez **Y**, puis appuyez sur **Entrée** pour enregistrer le fichier sous myscript.sh.
6. À partir de l’invite du Cloud Shell, marquez le fichier comme exécutable avec la commande `chmod +x myscript.sh`.
7. Exécutez le script en entrant `./myscript.sh`.
8. Une fois l’exécution du script terminée, passez en revue les sous-réseaux pour le réseau virtuel en copiant et collant la commande suivante dans votre bash Cloud Shell :
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-cli) de cet article.

## <a name="powershell"></a>PowerShell
1. Installez la dernière version du module Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Démarrez une session PowerShell en cliquant sur le bouton Démarrer de Windows, en tapant **Powershell**, puis en cliquant sur **PowerShell** dans les résultats de la recherche.
3. Dans la fenêtre PowerShell, entrez la commande `login-azurermaccount` pour vous connecter avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Dans votre navigateur, copiez le script suivant :
    ```powershell
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Pour exécuter le script, cliquez avec le bouton droit dans la fenêtre PowerShell.
6. Passez en revue les sous-réseaux pour le réseau virtuel en copiant et collant la commande suivante dans la fenêtre PowerShell :
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-powershell) de cet article.

## <a name="template"></a>Modèle

Vous pouvez déployer un réseau virtuel avec un modèle Azure Resource Manager. Pour en savoir plus sur les modèles, voir [Qu’est-ce que Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Pour accéder au modèle et découvrir ses paramètres, accédez à la page web du modèle [Créer un réseau virtuel avec deux sous-réseaux](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Vous pouvez déployer le modèle à l’aide du [portail](#template-portal), de l’[interface de ligne de commande](#template-cli) ou de [PowerShell](#template-powershell).

**Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans toute sous-section de la section [Supprimer des ressources](#delete) de cet article.

### <a name="template-portal"></a>Portail

1. Dans votre navigateur, ouvrez la [page web](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets) du modèle.
2. Cliquez sur le bouton **Déployer dans Azure** pour ouvrir la page de connexion au portail Azure.
3. Connectez-vous au portail Azure avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Entrez les valeurs suivantes pour les paramètres :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Sélectionnez votre abonnement.|
    |Groupe de ressources|myResourceGroup|
    |Lieu|Sélectionnez un emplacement.|
    |Nom du réseau virtuel|MyVnet|
    |Préfixe d’adresse de réseau virtuel|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Public|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privé|

5. Acceptez les conditions générales, puis cliquez sur **Acheter** pour déployer le réseau virtuel.

### <a name="template-cli"></a>INTERFACE DE LIGNE DE COMMANDE

1. Dans un navigateur Internet, ouvrez le [portail](https://portal.azure.com) Azure et connectez-vous avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
2. En haut du portail, à droite de la barre *Rechercher des ressources*, cliquez sur l’icône **>_** pour démarrer un bash Azure Cloud Shell (version préliminaire). Le volet Cloud Shell s’affiche en bas du portail et, après quelques secondes, affiche une invite **username@Azure:~$**. Le Cloud Shell vous connecte automatiquement à Azure en utilisant les informations d’identification à l’aide desquelles vous vous êtes authentifié auprès du portail.
3. Créez un groupe de ressources pour le réseau virtuel en entrant la commande suivante : `az group create --name MyResourceGroup --location eastus`
4. Vous pouvez déployer le modèle avec :
    - **Les valeurs des paramètres par défaut :** entrez la commande suivante :  `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`.
    - **Les valeurs des paramètres personnalisées :** téléchargez et modifiez le modèle avant de le déployer, déployez le modèle avec des paramètres à partir de la ligne de commande ou déployez le modèle à l’aide d’un fichier de paramètres séparé. Vous pouvez télécharger les fichiers du modèle et des paramètre en cliquant sur le bouton **Parcourir sur GitHub** dans la page web du modèle [Créer un réseau virtuel avec deux sous-réseaux](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Dans GitHub, cliquez sur le fichier **azuredeploy.parameters.json** ou **azuredeploy.json**, puis cliquez sur le bouton **Raw** pour le fichier. Dans votre navigateur, copiez le contenu et enregistrez-le dans un fichier sur votre ordinateur. Modifiez les valeurs des paramètres dans le modèle, ou déployez le modèle avec le fichier de paramètres séparé.  

    Pour en savoir plus sur le déploiement de modèles à l’aide de ces méthodes, tapez `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Installez la dernière version du module Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/azure/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Démarrez une session PowerShell en cliquant sur le bouton Démarrer de Windows, en tapant **powershell**, puis en cliquant sur **PowerShell** dans les résultats de la recherche.
3. Dans la fenêtre PowerShell, entrez la commande `login-azurermaccount` pour vous connecter avec votre [compte](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) Azure. Si vous n’avez pas de compte, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Créer un groupe de ressources pour le réseau virtuel en entrant la commande suivante : `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. Vous pouvez déployer le modèle avec :
    - **Les valeurs des paramètres par défaut :** pour ce faire, entrez la commande suivante :  `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`.        
    - **Les valeurs des paramètres personnalisées :** pour ce faire téléchargez et modifiez le modèle avant de le déployer, déployez le modèle avec des paramètres à partir de la ligne de commande ou déployez le modèle à l’aide d’un fichier de paramètres séparé. Vous pouvez télécharger les fichiers du modèle et des paramètre en cliquant sur le bouton **Parcourir sur GitHub** dans la page web du modèle [Créer un réseau virtuel avec deux sous-réseaux](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Dans GitHub, cliquez sur le fichier **azuredeploy.parameters.json** ou **azuredeploy.json**, puis cliquez sur le bouton **Raw** pour le fichier. Dans votre navigateur, copiez le contenu et enregistrez-le dans un fichier sur votre ordinateur. Modifiez les valeurs des paramètres dans le modèle, ou déployez le modèle avec le fichier de paramètres séparé.  

    Pour en savoir plus sur le déploiement de modèles à l’aide de ces méthodes, tapez `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Supprimer des ressources
Après avoir suivi ce didacticiel, vous pouvez supprimer les ressources de façon à ne pas exposer de frais d’utilisation. La suppression d’un groupe de ressources a pour effet de supprimer toutes les ressources qu’il contient.

### <a name="delete-portal"></a>Portail

1. Dans le portail, dans la zone *Rechercher des ressources* située en haut, commencez à taper *MyResourceGroup*. Lorsque **MyResourceGroup** apparaît dans les résultats de recherche, cliquez dessus.
2. Dans le panneau de MyResourceGroup qui s’affiche, cliquez sur l’icône Supprimer située en haut.
3. Pour confirmer la suppression, dans la zone **TAPEZ LE NOM DU GROUPE DE RESSOURCES :**, entrez *MyResourceGroup*, puis cliquez sur **Supprimer**.

### <a name="delete-cli"></a>INTERFACE DE LIGNE DE COMMANDE

À l’invite du Cloud Shell, entrez la commande suivante :`az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

À l’invite de PowerShell, entrez la commande suivante :`Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>Étapes suivantes

- Pour comprendre tous les paramètres de réseau virtuel et de sous-réseau, lisez les articles [Gérer un réseau virtuel](virtual-network-manage-network.md#view-vnet) et [Gérer un sous-réseau](virtual-network-manage-subnet.md#create-subnet). Diverses options permettent de créer des réseaux virtuels et sous-réseaux de production répondant à différentes exigences.
- Filtrez le trafic de sous-réseau entrant et sortant en créant et en appliquant des [groupes de sécurité réseau](virtual-networks-nsg.md) (NSG) aux sous-réseaux.
- Créez une machine virtuelle [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et connectez-la au réseau virtuel.
- Connectez le réseau virtuel à un autre réseau virtuel situé dans le même emplacement avec [VNET Peering](virtual-network-peering-overview.md) (Appairage VNet).
- Connectez le réseau virtuel à un réseau local à l’aide d’un [réseau privé virtuel (VPN) site à site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou d’un circuit [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

