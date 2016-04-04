<properties
 pageTitle="Créer un nœud principal HPC Pack dans une machine virtuelle Azure | Microsoft Azure"
 description="Apprenez à utiliser le portail Azure et le modèle de déploiement Resource Manager pour créer un nœud principal Microsoft HPC Pack dans une machine virtuelle Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Créer le nœud principal d’un cluster HPC Pack dans une machine virtuelle Azure avec une image Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Cet article vous montre comment utiliser une [image de machine virtuelle Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) dans Azure Marketplace pour créer le nœud principal d’un cluster HPC à l’aide du portail Azure. Cette image de machine virtuelle HPC Pack est basée sur Windows Server 2012 R2 Datacenter avec HPC Pack 2012 R2 Update 3 préinstallé. Utilisez ce nœud principal pour un déploiement preuve de concept de HPC Pack dans Azure. Vous pouvez ensuite ajouter des ressources de calcul au cluster pour exécuter des charges de travail HPC.


![Nœud principal HPC Pack][headnode]

>[AZURE.TIP]Pour un déploiement de production d’un cluster HPC Pack complet dans Azure, nous vous recommandons d’utiliser une méthode automatisée, comme le [script de déploiement IaaS HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) ou un modèle Resource Manager comme le modèle [Cluster HPC Pack pour charges de travail Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Consultez [Options de cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-options.md) pour obtenir d’autres modèles.


## Considérations en matière de planification

* **Domaine Active Directory** : le nœud principal HPC Pack doit être joint à un domaine Active Directory dans Azure avant de démarrer les services HPC sur la machine virtuelle. Comme indiqué dans cet article, pour un déploiement preuve de concept, vous pouvez promouvoir la machine virtuelle que vous créez pour le nœud principal en tant que contrôleur de domaine avant de démarrer les services HPC. Une autre option consiste à déployer un contrôleur de domaine distinct et une forêt dans Azure auxquels vous joignez la machine virtuelle.

* **Réseau virtuel Azure** : comme indiqué dans cet article, quand vous déployez le nœud principal HPC Pack à l’aide du modèle de déploiement Resource Manager dans le portail Azure, vous spécifiez ou créez un réseau virtuel Azure (VNet). Vous devez utiliser ce réseau virtuel ultérieurement pour ajouter des machines virtuelles de nœud de calcul de cluster au cluster HPC, ou si vous joignez le nœud principal à un domaine Active Directory existant.

    
## Étapes pour créer le nœud principal

Voici les étapes précises pour créer une machine virtuelle Azure pour le nœud principal HPC Pack à l’aide du modèle de déploiement Resource Manager dans le portail Azure.


1. Si vous voulez créer une forêt Active Directory dans Azure avec des machines virtuelles de contrôleur de domaine distinctes, une option consiste à utiliser un [modèle Resource Manager](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Pour un simple déploiement preuve de concept, il est acceptable d’omettre cette option et de configurer la machine virtuelle du nœud principal elle-même en tant que contrôleur de domaine comme décrit dans une étape ultérieure.
    
2. Dans le [portail Azure](https://portal.azure.com), sélectionnez l’image HPC Pack 2012 R2 dans Azure Marketplace. Pour cela, cliquez sur **Nouveau** et recherchez **HPC Pack** dans Marketplace. Sélectionnez **HPC Pack 2012 R2 sur Windows Server 2012 R2**.

3. Dans la page **HPC Pack 2012 R2 sur Windows Server 2012 R2**, sélectionnez le modèle de déploiement **Resource Manager**, puis cliquez sur **Créer**.

    ![Image HPC Pack][marketplace]

4. Utilisez le portail pour configurer les paramètres et créer la machine virtuelle. Pour obtenir des instructions détaillées, suivez le didacticiel [Créer une machine virtuelle Windows dans le portail Azure](virtual-machines-windows-hero-tutorial.md). Pour un premier déploiement, vous pouvez généralement accepter de nombreux paramètres par défaut ou recommandés.

    **Considérations liées au réseau virtuel**

   * Si vous créez un réseau virtuel dans **Paramètres**, spécifiez une plage d’adresses de réseau privé comme 10.0.0.0/16 et une plage d’adresses de sous-réseau comme 10.0.0.0/24.
    
4. Après avoir créé la machine virtuelle et une fois qu’elle est en cours d’exécution, [connectez-vous à la machine virtuelle](virtual-machines-windows-connect-logon.md). 

5. Joignez la machine virtuelle à une forêt de domaines existante ou créez une forêt de domaines sur la machine virtuelle elle-même.

    **Considérations liées aux domaines Active Directory**

    * Si vous avez créé la machine virtuelle dans un réseau virtuel Azure avec une forêt de domaines existante, utilisez des outils Gestionnaire de serveur ou Windows PowerShell standard pour la joindre à la forêt de domaines. Ensuite, redémarrez.

    * Si vous avez créé la machine virtuelle dans un réseau virtuel sans forêt de domaines existante, promouvez-la en tant que contrôleur de domaine. Pour cela, utilisez des outils Gestionnaire de serveur ou Windows PowerShell standard pour installer et configurer le rôle Services de domaine Active Directory. Pour obtenir la procédure détaillée, consultez [Installer une nouvelle forêt Active Directory Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).

5. Une fois que la machine virtuelle est en cours d’exécution et jointe à une forêt Active Directory, démarrez les services HPC Pack sur le nœud principal. Pour ce faire :

    a. Connectez-vous à la machine virtuelle à l’aide d’un compte de domaine membre du groupe Administrateurs local. Par exemple, vous pouvez utiliser le compte administrateur que vous avez configuré lorsque vous avez créé la machine virtuelle du nœud principal.

    b. Pour une configuration du nœud principal par défaut, démarrez Windows PowerShell en tant qu’administrateur et tapez la commande suivante :

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Le démarrage des services HPC Pack peut prendre plusieurs minutes.

    Pour d’autres options de configuration du nœud principal, tapez `get-help HPCHNPrepare.ps1`.


## Étapes suivantes

* Vous pouvez à présent utiliser le nœud principal de votre cluster HPC Pack. Par exemple, démarrez HPC Cluster Manager et procédez à la [liste des tâches de déploiement](https://technet.microsoft.com/library/jj884141.aspx).
* Ajoutez des [nœuds d’extension Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) dans un service cloud pour augmenter la capacité de calcul du cluster à la demande. 

* Essayez d’exécuter une charge de travail test sur le cluster. Pour obtenir un exemple, consultez le [guide de mise en route](https://technet.microsoft.com/library/jj884144) du HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png

<!---HONumber=AcomDC_0323_2016-->