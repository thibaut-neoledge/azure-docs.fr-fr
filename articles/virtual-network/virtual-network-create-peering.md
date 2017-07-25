---
title: "Créer une homologation de réseaux virtuels Azure | Microsoft Docs"
description: "Apprenez à créer une homologation de réseaux virtuels entre deux réseaux virtuels."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: jdial;narayan;annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 666165115e80a39d02386719b0d7e64d7ae17749
ms.contentlocale: fr-fr
ms.lasthandoff: 06/09/2017


---
# <a name="create-a-virtual-network-peering"></a>Créer une homologation de réseaux virtuels

Dans ce didacticiel, vous apprenez à créer une homologation de réseaux virtuels entre deux réseaux virtuels. Homologuer deux réseaux virtuels permet aux ressources de différents réseaux virtuels de communiquer entre elles avec la même bande passante et latence, comme si les ressources se trouvaient sur le même réseau virtuel. Une homologation de réseaux virtuels ne peut être créée qu’entre deux réseaux virtuels qui existent dans la même région Azure. Pour en savoir plus sur l’homologation de réseaux virtuels, lisez l’article de présentation [Homologation de réseaux virtuels](virtual-network-peering-overview.md). 

Si vous avez besoin de connecter des réseaux virtuels qui existent dans différentes régions Azure, vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure. 

Vous pouvez utiliser le [portail Azure](#portal), Azure [PowerShell](#powershell), [l’interface de ligne de commande](#cli) Azure ou le [modèle Azure Resource Manager](#template) pour créer une homologation de réseaux virtuels. Cliquez sur les liens des outils précédents pour accéder directement à la procédure permettant de créer une homologation de réseaux virtuels à l’aide de l’outil de votre choix.

## <a name="portal"></a>Créer une homologation - portail Azure

Procédez comme suit pour créer une homologation de réseaux virtuels entre deux réseaux virtuels déployés via Resource Manager qui existent dans le même abonnement. Vous pouvez également [homologuer deux réseaux virtuels dans différents abonnements](#different-subscriptions) ou [homologuer un réseau virtuel (Resource Manager) avec un réseau virtuel (classique)](#different-models). 

1. Connectez-vous au [portail Azure](https://portal.azure.com). Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Consultez la section [Autorisations](#permissions) de cet article pour plus d’informations.
2. Cliquez sur **+ Nouveau**, puis sur **Mise en réseau** et **Réseau virtuel**.
3. Dans le panneau **Réseau virtuel**, puis dans le champ **Sélectionner un modèle de déploiement**, laissez l’option **Resource Manager** sélectionnée, puis cliquez sur **Créer**.
4. Dans le panneau **Créer un réseau virtuel**, entrez ou sélectionnez les valeurs des paramètres suivants, puis cliquez sur **Créer** :
    - **Nom** : *myVnet1*
    - **Espace d’adressage** : *10.0.0.0/16*
    - **Nom du sous-réseau** : *par défaut*
    - **Espace d’adressage de sous-réseau** : *10.0.0.0/24*
    - **Abonnement** : sélectionnez votre abonnement
    - **Groupe de ressources** : sélectionnez **Créer** et entrez *myResourceGroup*
    - **Emplacement** : *États-Unis de l’Est*
5. Répétez les étapes 2 à 4 en spécifiant les valeurs suivantes à l’étape 4 :
    - **Nom** : *myVnet2*
    - **Espace d’adressage** : *10.1.0.0/16*
    - **Nom du sous-réseau** : *par défaut*
    - **Espace d’adressage de sous-réseau** : *10.1.0.0/24*
    - **Abonnement** : sélectionnez votre abonnement
    - **Groupe de ressources** : sélectionnez **Use existing** (Utiliser existant), puis *myResourceGroup*
    - **Emplacement** : *États-Unis de l’Est*
6. Dans le champ **Rechercher des ressources** située en haut du portail, tapez *myResourceGroup*. Cliquez sur **myResourceGroup** quand il apparaît dans les résultats de la recherche. Un panneau s’affiche pour le groupe de ressources **myresourcegroup**. Le groupe de ressources contient les deux réseaux virtuels créés dans les étapes précédentes.
7. Cliquez sur **myVNet1**.
8. Dans le panneau **myVnet1** qui s’affiche, cliquez sur **Homologations** dans la liste verticale des options sur le côté gauche du panneau.
9. Dans le panneau **myVnet1 - Homologations** qui s’est affiché, cliquez sur **+ Ajouter**.
10. Dans le panneau **Ajouter l’homologation** qui s’affiche, entrez ou sélectionnez les options suivantes et cliquez sur **OK** :
     - **Nom** : *myVnet1ToMyVnet2*
     - **Modèle de déploiement de réseau virtuel** : sélectionnez **Resource Manager**. 
     - **Abonnement** : sélectionnez votre abonnement
     - **Réseau virtuel** : cliquez sur **Choisir un réseau virtuel**, puis sur **myVnet2**.
     - **Autoriser l’accès au réseau virtuel :** vérifiez que l’option **Activé** est sélectionnée.
    Il n’y aucun autre paramètre utilisé dans ce didacticiel. Pour en savoir plus sur tous les paramètres d’homologation, consultez [Create, change, or delete a virtual network peering](virtual-network-manage-peering.md#create-peering) (Créer, modifier ou supprimer une homologation de réseaux virtuels).
11. Après avoir cliqué sur **OK** à l’étape précédente, le panneau **Ajouter l’homologation** se ferme et vous pouvez à nouveau voir le panneau **myVnet1 - Homologations**. Après quelques secondes, l’homologation que vous avez créée apparaît dans le panneau. **Lancé** est indiqué dans la colonne **ÉTAT D’APPAIRAGE** pour l’homologation **myVnet1ToMyVnet2** que vous avez créée. Vous avez effectué une homologation de Vnet1 à Vnet2, mais vous devez maintenant effectuer une homologation de Vnet2 à Vnet1. L’homologation doit être créée dans les deux directions pour permettre aux ressources des réseaux virtuels de communiquer entre elles.
12. Répétez les étapes 6 à 11 pour myVnet2.  Nommez l’homologation *myVnet2ToMyVnet1*.
13. Quelques secondes après avoir cliqué sur **OK** pour créer l’homologation pour MyVnet2, l’homologation **myVnet2ToMyVnet1** que vous venez de créer est répertoriée avec **Connecté** dans la colonne **ÉTAT D’APPAIRAGE**.
14. Répétez les étapes 6 à 8 pour MyVnet1. **L’ÉTAT D’APPAIRAGE** pour l’homologation **myVnet1ToVNet2** est également défini sur **Connecté** maintenant. L’homologation est établie avec succès une fois que vous voyez **Connecté** dans la colonne **ÉTAT D’APPAIRAGE** pour les deux réseaux virtuels de l’homologation.
15. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
16. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-portal) de cet article.

Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Créer une homologation - interface de ligne de commande Azure

Le script suivant :

- Requiert l’interface de ligne de commande Azure version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Fonctionne dans un interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

1. Créez un groupe de ressources et deux réseaux virtuels.
    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    #
    ```
2. Créez une homologation de réseaux virtuels entre les deux réseaux virtuels.
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    #
    ```
3. Après l’exécution du script, passez en revue les homologations pour chaque réseau virtuel. Copiez la commande suivante et collez-la dans votre fenêtre de commande :

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    #
    ```
Exécutez une nouvelle fois la commande précédente, en remplaçant *myVnet1* par *myVnet2*. La sortie des deux commandes affiche **Connecté** dans la colonne **PeeringState**.
4. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
5. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-cli) de cet article.

Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
 
## <a name="powershell"></a>Créer une homologation - PowerShell

1. Installez la dernière version du module PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, consultez [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Vue d’ensemble d’Azure PowerShell).
2. Pour démarrer une session PowerShell, accédez à **Démarrer**, entrez **powershell**, puis cliquez sur **PowerShell**.
3. Dans la fenêtre PowerShell, connectez-vous à Azure en entrant la commande `login-azurermaccount`. Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Consultez la section [Autorisations](#permissions) de cet article pour plus d’informations.
4. Dans votre navigateur, copiez le script suivant, puis cliquez avec le bouton droit dans votre fenêtre PowerShell pour exécuter le script qui crée un groupe de ressources et deux réseaux virtuels :
    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    #
    ```
5. Dans votre navigateur, copiez le script suivant, puis cliquez avec le bouton droit dans votre fenêtre PowerShell pour exécuter le script qui crée une homologation de réseaux virtuels entre les deux réseaux virtuels :
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    #
    ```
6. Pour passer en revue les sous-réseaux du réseau virtuel, copiez la commande suivante, puis collez-la dans la fenêtre PowerShell :

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    #
    ```

Exécutez une nouvelle fois la commande précédente, en remplaçant *myVnet1* par *myVnet2*. La sortie des deux commandes affiche **Connecté** dans la colonne **PeeringState**.
7. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
8. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-powershell) de cet article.

Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Créer une homologation - modèle Resource Manager

1. Faites référence au modèle Azure Resource Manager [Créer une homologation de réseaux virtuels](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering). Des instructions sont fournies avec le modèle pour le déployer à l’aide du portail Azure, de PowerShell ou de l’interface de ligne de commande Azure. Connectez-vous à l’outil de votre choix pour déployer le modèle avec un compte qui dispose des autorisations nécessaires pour créer une homologation de réseaux virtuels. Consultez la section [Autorisations](#permissions) de cet article pour plus d’informations.
2. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
3. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete) de cet article, soit avec le portail Azure, PowerShell ou l’interface de ligne de commande Azure.

## <a name="different-models"></a>Homologuer les réseaux virtuels créés via des modèles de déploiement différents

Les étapes décrites dans les sections précédentes ont expliqué comment créer une homologation de réseaux virtuels entre deux réseaux virtuels créés via le modèle de déploiement Resource Manager. Vous pouvez également créer une homologation entre un réseau virtuel (Resource Manager) et un réseau virtuel (classique). Vous ne pouvez pas créer d’homologation de réseaux virtuels entre deux réseaux virtuels créés via le modèle de déploiement classique. Pour en savoir plus sur les modèles de déploiement Azure, consultez l’article [Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pour créer l’homologation de réseaux virtuels, effectuez les étapes 1 à 11 de la section [Portail](#portal) de cet article, mais choisissez le modèle de déploiement **classique** lors de la création de myVnet2 à l’étape 3.

Lorsque vous créez une homologation de réseaux virtuels entre un réseau virtuel (Resource Manager) et un réseau virtuel (classique), vous configurez uniquement l’homologation pour le réseau virtuel (classique) à partir du réseau virtuel (Resource Manager). Après avoir créé l’homologation pour le réseau virtuel (Resource Manager), son état d’appairage est **Mise à jour en cours**. L’état passe automatiquement à **Connecté** au bout de quelques secondes, car vous n’avez pas besoin de créer d’homologation pour le réseau virtuel (classique). 

> [!NOTE]
> Bien que cela ne soit pas abordé dans ce didacticiel, vous pouvez ajuster les scripts des sections [Interface de ligne de commande Azure](#cli) et [PowerShell](#powershell) de cet article pour illustrer les différences traitées dans cette section.

## <a name="different-subscriptions"></a>Homologuer des réseaux virtuels de différents abonnements

Les étapes décrites dans les sections précédentes ont expliqué comment créer une homologation de réseaux virtuels entre deux réseaux virtuels dans le même abonnement. Vous pouvez également créer une homologation entre des réseaux virtuels dans différents abonnements, à condition que les abonnements soient associés au même locataire Azure Active Directory. Si vous n’avez pas encore de locataire AD, vous pouvez rapidement en [créer un](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch). Si les abonnements sont associés à différents locataires Active Directory, vous ne pouvez pas créer d’homologation de réseaux virtuels entre des réseaux virtuels dans différents abonnements. Les étapes pour créer l’homologation sont légèrement différentes, selon le modèle de déploiement via lequel les réseaux virtuels ont été créés, comme suit :

### <a name="different-subscriptions-same-deployment-model"></a>Deux réseaux virtuels créés via Resource Manager

1. Effectuez les étapes 1 à 7 de la section [Portail](#portal) de cet article, mais choisissez un autre abonnement (associé au même locataire Azure Active Directory) pour myVnet2 à l’étape 5 que celui que vous choisissez pour myVnet1 à l’étape 4.
2. Dans le panneau **myVnet1** qui s’affiche, cliquez sur **Contrôle d’accès (IAM)** dans la liste verticale des options sur le côté gauche du panneau.
3. Dans le panneau **myVnet1 - Contrôle d’accès (IAM)** qui s’affiche, cliquez sur **+ Ajouter**.
4. Dans le panneau **Ajouter les autorisations** qui s’affiche, sélectionnez **Contributeur de réseaux** dans le champ **Rôle**.
5. Dans le champ **Sélectionner**, sélectionnez un nom d’utilisateur ou tapez une adresse e-mail pour en rechercher un. La liste des utilisateurs affichée provient du même locataire Azure Active Directory que le réseau virtuel pour lequel vous configurez l’homologation.
6. Cliquez sur **Save**.
7. Dans le panneau **myVnet1 - Contrôle d’accès (IAM)**, cliquez sur **Propriétés** dans la liste verticale des options sur le côté gauche du panneau. Copiez **l’ID DE RESSOURCE**, qui ressemble à l’exemple suivant : /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet1.
8. Effectuez les étapes 1 à 7 de cette section pour myVnet2.
9. Pour vous assurer que l’autorisation est bien activée, déconnectez-vous d’Azure, puis reconnectez-vous. Si vous avez utilisé des comptes différents pour les deux réseaux virtuels, déconnectez-vous des deux comptes et reconnectez-vous.
10. Dans le champ **Rechercher des ressources** située en haut du portail, tapez *myResourceGroup*. Cliquez sur **myResourceGroup** quand il apparaît dans les résultats de la recherche. Un panneau s’affiche pour le groupe de ressources **myresourcegroup**. Le groupe de ressources contient les deux réseaux virtuels créés dans les étapes précédentes.
11. Cliquez sur **myVNet1**.
12. Dans le panneau **myVnet1** qui s’affiche, cliquez sur **Homologations** dans la liste verticale des options sur le côté gauche du panneau.
13. Dans le panneau **myVnet1 - Homologations** qui s’est affiché, cliquez sur **+ Ajouter**.
14. Dans le panneau **Ajouter l’homologation** qui s’affiche, entrez ou sélectionnez les options suivantes et cliquez sur **OK** :
     - **Nom** : *myVnet1ToMyVnet2*
     - **Modèle de déploiement de réseau virtuel** : sélectionnez **Classique**. 
     - **Je connais mon ID de ressource** : cochez cette case.
     - **ID de ressource** : entrez l’ID de ressource pour myVnet2. Ce champ s’affiche uniquement quand la case **Je connais mon ID de ressource** est cochée.
     - **Autoriser l’accès au réseau virtuel :** vérifiez que l’option **Activé** est sélectionnée.
    Il n’y aucun autre paramètre utilisé dans ce didacticiel. Pour en savoir plus sur tous les paramètres d’homologation, consultez [Create, change, or delete a virtual network peering](virtual-network-manage-peering.md#create-peering) (Créer, modifier ou supprimer une homologation de réseaux virtuels).
15. Après avoir cliqué sur **OK** à l’étape précédente, le panneau **Ajouter l’homologation** se ferme et vous pouvez à nouveau voir le panneau **myVnet1 - Homologations**. Après quelques secondes, l’homologation que vous avez créée apparaît dans le panneau. **Lancé** est indiqué dans la colonne **ÉTAT D’APPAIRAGE** pour l’homologation **myVnet1ToMyVnet2** que vous avez créée. Vous avez effectué une homologation de Vnet1 à Vnet2, mais vous devez maintenant effectuer une homologation de Vnet2 à Vnet1. L’homologation doit être créée dans les deux directions pour permettre aux ressources des réseaux virtuels de communiquer entre elles.
16. Répétez les étapes 10 à 14 de cette section pour myVnet2.  Nommez l’homologation *myVnet2ToMyVnet1* et entrez **l’ID de ressource** pour myVnet1.
17. Quelques secondes après avoir cliqué sur **OK** pour créer l’homologation pour MyVnet2, l’homologation **myVnet2ToMyVnet1** que vous venez de créer est répertoriée avec **Connecté** dans la colonne **ÉTAT D’APPAIRAGE**.
18. Répétez les étapes 10 à 11 de cette section pour MyVnet1. **L’ÉTAT D’APPAIRAGE** pour l’homologation **myVnet1ToVNet2** est également défini sur **Connecté** maintenant. L’homologation est établie avec succès une fois que vous voyez **Connecté** dans la colonne **ÉTAT D’APPAIRAGE** pour les deux réseaux virtuels de l’homologation.
19. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
20. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-portal) de cet article.

### <a name="different-subscriptions-different-deployment-models"></a>Un réseau virtuel (Resource Manager), un réseau virtuel (classique)
 
[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-preview.md)]

1. Inscrivez-vous à la préversion. Vous ne pouvez pas homologuer des réseaux virtuels créés via des modèles de déploiement différents qui se trouvent dans des abonnements distincts tant que vous n’êtes pas inscrit à la préversion. Vous ne pouvez pas vous inscrire à la préversion dans le portail ou à l’aide de l’interface de ligne de commande Azure. Vous pouvez uniquement vous inscrire à la préversion à l’aide de PowerShell. Pour vous inscrire à la préversion, effectuez les tâches suivantes :
    - Installez la dernière version du module PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/). Si vous débutez dans l’utilisation d’Azure PowerShell, consultez [Overview of Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) (Vue d’ensemble d’Azure PowerShell).
    - Pour démarrer une session PowerShell à partir d’un PC Windows, accédez à **Démarrer**, entrez **powershell**, puis cliquez sur **PowerShell**.
    - Dans la fenêtre PowerShell, connectez-vous à Azure en entrant la commande `login-azurermaccount`. Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Consultez la section [Autorisations](#permissions) de cet article pour plus d’informations.
    - Inscrivez-vous à la fonction en préversion pour vos abonnements Azure et entrez les commandes suivantes à partir de PowerShell : 
    
    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    #
    ```
    Inscrivez-vous à la préversion tout en étant connecté à chaque abonnement. N’effectuez pas les étapes suivantes tant que la sortie **RegistrationState** que vous recevez après avoir entré la commande suivante est définie sur **Inscrit** dans les deux abonnements.

    ```powershell
    Get-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    #
    ```

2. Effectuez les étapes 1 à 7 de la section [Portail](#portal) de cet article en apportant les changements suivants :
    - Choisissez un autre abonnement (associé au même locataire Azure Active Directory) pour myVnet2 à l’étape 5 que celui que vous choisissez pour myVnet1 à l’étape 4.
    - Choisissez **Classique** à l’étape 3 lors de la création de myVnet2.
3. Dans le champ **Rechercher des ressources** située en haut du portail, tapez *myResourceGroup*. Cliquez sur **myResourceGroup** quand il apparaît dans les résultats de la recherche. Un panneau s’affiche pour le groupe de ressources **myresourcegroup**. Le groupe de ressources contient les deux réseaux virtuels créés dans les étapes précédentes.
4. Cliquez sur **myVNet1**.
5. Effectuez les étapes 2 à 9 de la section [Deux réseaux virtuels créés via Resource Manager](#different-subscriptions-same-deployment-model) de cet article.
6. Dans le champ **Rechercher des ressources** située en haut du portail, tapez *myResourceGroup*. Cliquez sur **myResourceGroup** quand il apparaît dans les résultats de la recherche. Un panneau s’affiche pour le groupe de ressources **myresourcegroup**. Le groupe de ressources contient les deux réseaux virtuels créés dans les étapes précédentes.
7. Cliquez sur **myVNet1**.
8. Dans le panneau **myVnet1** qui s’affiche, cliquez sur **Homologations** dans la liste verticale des options sur le côté gauche du panneau.
9. Dans le panneau **myVnet1 - Homologations** qui s’est affiché, cliquez sur **+ Ajouter**.
10. Dans le panneau **Ajouter l’homologation** qui s’affiche, entrez ou sélectionnez les options suivantes et cliquez sur **OK** :
     - **Nom** : *myVnet1ToMyVnet2*
     - **Modèle de déploiement de réseau virtuel** : sélectionnez **Classique**. 
     - **Je connais mon ID de ressource** : cochez cette case.
     - **ID de ressource** : entrez l’ID de ressource pour myVnet2. Ce champ s’affiche uniquement quand la case **Je connais mon ID de ressource** est cochée.
     - **Autoriser l’accès au réseau virtuel :** vérifiez que l’option **Activé** est sélectionnée.
    Il n’y aucun autre paramètre utilisé dans ce didacticiel. Pour en savoir plus sur tous les paramètres d’homologation, consultez [Create, change, or delete a virtual network peering](virtual-network-manage-peering.md#create-peering) (Créer, modifier ou supprimer une homologation de réseaux virtuels).

    Lorsque vous créez une homologation de réseaux virtuels entre un réseau virtuel (Resource Manager) et un réseau virtuel (classique), vous configurez uniquement l’homologation pour le réseau virtuel (classique) à partir du réseau virtuel (Resource Manager). Après avoir créé l’homologation pour le réseau virtuel (Resource Manager), son état d’appairage est **Mise à jour en cours**. L’état passe automatiquement à **Connecté** au bout de quelques secondes, car vous n’avez pas besoin de créer d’homologation pour le réseau virtuel (classique). 
11. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce didacticiel, vous pouvez créer une machine virtuelle dans chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
12. **Facultatif :** pour supprimer les ressources créées dans ce didacticiel, procédez de la manière décrite dans la section [Supprimer des ressources](#delete-portal) de cet article.

> [!NOTE]
> Bien que cela ne soit pas abordé dans ce didacticiel, vous pouvez ajuster les scripts des sections [Interface de ligne de commande Azure](#cli) et [PowerShell](#powershell) de cet article pour illustrer les différences traitées dans cette section.

## <a name="permissions"></a>Autorisations

Les comptes que vous utilisez pour créer une homologation de réseaux virtuels doivent avoir les autorisations ou le rôle nécessaires. Par exemple, si vous avez effectué une homologation entre deux réseaux virtuels nommés VNet1 et VNet2, votre compte doit posséder les autorisations ou le rôle minimum suivants pour chaque réseau virtuel :
    
|Réseau virtuel|Modèle de déploiement|Rôle|Autorisations|
|---|---|---|---|
|VNet1|Gestionnaire de ressources|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classique|[Collaborateur de réseau classique](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|VNet2|Gestionnaire de ressources|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classique|[Collaborateur de réseau classique](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Apprenez-en davantage sur les [rôles intégrés](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) et l’assignation des autorisations spécifiques aux [rôles personnalisés](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Resource Manager uniquement).

## <a name="delete"></a>Supprimer des ressources
Lorsque vous aurez terminé ce didacticiel, vous souhaiterez peut-être supprimer les ressources que vous avez créées, afin que leur utilisation ne soit pas facturée. La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

### <a name="delete-portal"></a>Portail Azure

1. Dans le champ de recherche du portail, entrez **myResourceGroup**. Dans les résultats de la recherche, cliquez sur **myResourceGroup**.
2. Dans le panneau **myResourceGroup**, cliquez sur l’icône **Supprimer**.
3. Pour confirmer la suppression, dans le champ **TAPEZ LE NOM DU GROUPE DE RESSOURCES :**, entrez **myResourceGroup**, puis cliquez sur **Supprimer**.

### <a name="delete-cli"></a>Interface CLI Azure

Dans un interpréteur de commandes Linux, macOS ou Windows, entrez la commande suivante. Vous pouvez également cliquer sur le bouton Essayer en haut à droite du bloc de code suivant pour lancer Cloud Shell. Ensuite, utilisez le bouton Copier pour copier et coller l’exemple de code dans Cloud Shell.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Dans l’invite de commandes PowerShell, entrez la commande suivante :

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- Familiarisez-vous bien avec les [comportements et contraintes importants de l’homologation de réseaux virtuels](virtual-network-manage-peering.md#about-peering) avant d’en créer une pour une utilisation en production.
- Apprenez-en davantage sur tous les [paramètres d’homologation de réseaux virtuels](virtual-network-manage-peering.md#create-peering).
- Découvrez comment [créer une topologie de réseau de type hub et spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) avec l’homologation de réseaux virtuels.

