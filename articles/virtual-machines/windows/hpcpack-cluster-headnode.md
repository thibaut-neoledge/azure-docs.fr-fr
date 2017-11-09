---
title: "Créer un nœud principal HPC Pack dans une machine virtuelle Azure | Microsoft Docs"
description: "Apprenez à utiliser le portail Azure et le modèle de déploiement Resource Manager pour créer un nœud principal Microsoft HPC Pack 2012 R2 dans une machine virtuelle Azure."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: b2bb9caf82a580dc5f67ea0b0b1c2e9a46363e9c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Créer le nœud principal d’un cluster HPC Pack dans une machine virtuelle Azure avec une image Marketplace
Utilisez une [image de machine virtuelle Microsoft HPC Pack 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) à partir de la Place de marché Azure et du portail Azure pour créer le nœud principal d’un cluster HPC. Cette image de machine virtuelle HPC Pack est basée sur Windows Server 2012 R2 Datacenter avec HPC Pack 2012 R2 Update 3 préinstallé. Utilisez ce nœud principal pour un déploiement preuve de concept de HPC Pack dans Azure. Vous pouvez ensuite ajouter des nœuds de calcul au cluster pour exécuter des charges de travail HPC.

> [!TIP]
> Pour déployer un cluster HPC Pack 2012 R2 complet dans Azure, qui inclut le nœud principal et des nœuds de calcul, nous vous recommandons d’utiliser une méthode automatisée. Les options incluent le [script de déploiement du HPC Pack IaaS](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) et des modèles Resource Manager, tels que le [cluster HPC Pack pour les charges de travail Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Des modèles Resource Manager sont également disponibles pour les [clusters Microsoft HPC Pack 2016](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Considérations en matière de planification
Comme indiqué dans l’illustration suivante, vous déployez le nœud principal HPC Pack dans un domaine Active Directory sur un réseau virtuel Azure.

![Nœud principal HPC Pack][headnode]

* **Domaine Active Directory** : vous devez joindre le nœud principal HPC Pack 2012 R2 à un domaine Active Directory dans Azure avant de démarrer les services HPC sur la machine virtuelle. Comme indiqué dans cet article, pour un déploiement preuve de concept, vous pouvez promouvoir la machine virtuelle que vous créez pour le nœud principal en tant que contrôleur de domaine avant de démarrer les services HPC. Une autre option consiste à déployer un contrôleur de domaine et une forêt distincts dans Azure, auxquels vous joignez la machine virtuelle du nœud principal.

* **Modèle de déploiement** : pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle de déploiement Resource Manager. Cet article suppose que vous utilisez ce modèle de déploiement.

* **Réseau virtuel Azure** : lorsque vous utilisez le modèle de déploiement Resource Manager pour déployer le nœud principal, vous spécifiez ou créez un réseau virtuel Azure. Vous utilisez le réseau virtuel si vous devez joindre le nœud principal à un domaine Active Directory existant. Vous devez également ajouter par la suite des machines virtuelles de nœud de calcul au cluster.

## <a name="steps-to-create-the-head-node"></a>Étapes pour créer le nœud principal
Voici les étapes de niveau supérieur pour utiliser le portail Azure afin de créer une machine virtuelle Azure pour le nœud principal HPC Pack à l’aide du modèle de déploiement Resource Manager. 

1. Si vous voulez créer une forêt Active Directory dans Azure avec des machines virtuelles de contrôleur de domaine distinctes, une option consiste à utiliser un [modèle Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Pour un simple déploiement preuve de concept, il est acceptable d’omettre cette étape et de configurer la machine virtuelle du nœud principal elle-même en tant que contrôleur de domaine. Cette option est décrites plus loin dans cet article.
2. Sur la [page HPC Pack 2012 R2 sur Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) dans Azure Marketplace, cliquez sur **Créer une machine virtuelle**. 
3. Dans le portail, dans la page **HPC Pack 2012 R2 sur Windows Server 2012 R2**, sélectionnez le modèle de déploiement **Resource Manager**, puis cliquez sur **Créer**.
   
    ![Image HPC Pack][marketplace]
4. Utilisez le portail pour configurer les paramètres et créer la machine virtuelle. Si vous débutez avec Azure, suivez le didacticiel [Création d’une machine virtuelle Windows dans le portail Azure](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pour un déploiement preuve de concept, vous pouvez généralement accepter les paramètres par défaut ou recommandés.
   
   > [!NOTE]
   > Si vous souhaitez joindre le nœud principal à un domaine Active Directory existant dans Azure, spécifiez le réseau virtuel de ce domaine lors de la création de la machine virtuelle.
   > 
   > 
5. Après avoir créé la machine virtuelle, une fois que celle-ci est en cours d’exécution, [connectez-vous à la machine virtuelle](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) via le Bureau à distance. 
6. Pour joindre la machine virtuelle à une forêt de domaines Active Directory, choisissez l’une des options suivantes :
   
   * Si vous avez créé la machine virtuelle dans un réseau virtuel Azure avec une forêt de domaine existante, joignez la machine virtuelle à la forêt en vous servant des outils standard du Gestionnaire de serveur ou de Windows PowerShell. Ensuite, redémarrez.
   * Si vous avez créé la machine virtuelle dans un réseau virtuel (sans forêt de domaine existante), promouvez-la en tant que contrôleur de domaine. Suivez les étapes standard pour installer et configurer le rôle Services de domaine Active Directory sur le nœud principal. Pour obtenir la procédure détaillée, consultez [Installer une nouvelle forêt Active Directory Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).
7. Une fois la machine virtuelle en cours d’exécution et jointe à une forêt Active Directory, démarrez les services HPC Pack comme suit :
   
    a. Connectez-vous à la machine virtuelle du nœud principal en utilisant un compte de domaine membre du groupe Administrateurs local. Par exemple, utilisez le compte administrateur que vous avez configuré lorsque vous avez créé la machine virtuelle du nœud principal.
   
    b. Pour une configuration du nœud principal par défaut, démarrez Windows PowerShell en tant qu’administrateur et tapez la commande suivante :
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Le démarrage des services HPC Pack peut prendre plusieurs minutes.
   
    Pour d’autres options de configuration du nœud principal, tapez `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez à présent utiliser le nœud principal de votre cluster HPC Pack. Par exemple, démarrez HPC Cluster Manager et effectuez les tâches décrites dans la [liste des tâches de déploiement](https://technet.microsoft.com/library/jj884141.aspx).
* Si vous voulez augmenter la capacité de calcul du cluster à la demande, ajoutez des [nœuds d’extension Azure](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) dans un service cloud. 
* Essayez d’exécuter une charge de travail test sur le cluster. Pour obtenir un exemple, consultez le [guide de mise en route](https://technet.microsoft.com/library/jj884144)du HPC Pack.

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
