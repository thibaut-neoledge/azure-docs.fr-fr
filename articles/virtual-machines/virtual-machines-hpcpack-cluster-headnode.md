<properties
 pageTitle="Créer un nœud principal HPC Pack dans une machine virtuelle Azure | Microsoft Azure"
 description="Apprenez à utiliser le portail Azure Classic et le modèle de déploiement classique pour créer un nœud principal Microsoft HPC Pack dans une machine virtuelle Azure."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Créer le nœud principal d’un cluster HPC Pack dans une machine virtuelle Azure avec une image Marketplace

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Cet article vous montre comment utiliser l’[image de machine virtuelle Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) dans Azure Marketplace pour créer le nœud principal d’un cluster Windows HPC dans Azure dans le modèle de déploiement classique (gestion des services). Le nœud principal doit être joint à un domaine Active Directory dans un réseau virtuel Azure. Vous pouvez utiliser ce nœud principal pour une démonstration du déploiement du concept de HPC Pack dans Azure et ajouter des ressources de calcul au cluster pour exécuter des charges de travail HPC.


![Nœud principal HPC Pack][headnode]

>[AZURE.NOTE] Actuellement, l’image de machine virtuelle HPC Pack est basée sur Windows Server 2012 R2 Datacenter avec HPC Pack 2012 R2 Update 2 préinstallé. Microsoft SQL Server 2014 Express est également préinstallé.


Pour un déploiement de production d’un cluster HPC Pack dans Azure, nous vous recommandons une méthode de déploiement automatisé, telle que le [script de déploiement du HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md) ou un [modèle de démarrage rapide](https://azure.microsoft.com/documentation/templates/) du gestionnaire de ressources Azure.

## Considérations en matière de planification

* **Domaine Active Directory** : le nœud principal HPC Pack doit être joint à un domaine Active Directory dans Azure avant de démarrer les services HPC. Une option consiste à déployer un contrôleur de domaine distinct et une forêt déployée dans Azure à laquelle vous pouvez joindre la machine virtuelle. Pour une démonstration du déploiement du concept, vous pouvez promouvoir la machine virtuelle que vous créez pour le nœud principal en tant que contrôleur de domaine avant de démarrer les services HPC.

* **Réseau virtuel Azure** : si vous prévoyez d’ajouter des machines virtuelles à nœud de calcul de cluster au cluster HPC, ou si vous créez un contrôleur de domaine distinct pour le cluster, vous devez déployer le nœud principal dans un réseau virtuel Azure. Sans réseau virtuel, vous pouvez toujours ajouter des nœuds « d’extension » Azure au cluster.

## Étapes pour créer le nœud principal

Voici les étapes principales pour créer une machine virtuelle Azure pour le nœud principal HPC Pack. Vous pouvez utiliser divers outils Azure pour effectuer ces étapes dans le modèle de déploiement classique (Service Management) Azure.


1. Si vous envisagez de créer un réseau virtuel pour la machine virtuelle du nœud principal, consultez la rubrique [Créer un réseau virtuel (classique) à l’aide du portail Azure](../virtual-networks/virtual-networks-create-vnet-classic-portal.md).

    **Considérations**

    * Vous pouvez accepter la configuration par défaut de l’espace d’adressage du réseau virtuel et des sous-réseaux.

    * Si vous prévoyez d’utiliser une taille d’instance nécessitant beaucoup de ressources système (A8 – A11) pour le nœud principal HPC Pack ou d’ajouter ultérieurement des ressources de calcul au cluster, choisissez une région dans laquelle les instances sont disponibles. Lorsque vous utilisez des instances A8 ou A9 pour les charges de travail MPI, assurez-vous également que l’espace d’adressage du réseau virtuel ne chevauche pas celui réservé par le réseau RDMA dans Azure (172.16.0.0/12). Pour plus d’informations, consultez [À propos des instances nécessitant beaucoup de ressources système A8, A9, A10 et A11](virtual-machines-a8-a9-a10-a11-specs.md).

2. Si vous avez besoin de créer une nouvelle forêt Active Directory sur une machine virtuelle distincte, consultez [Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](../active-directory/active-directory-new-forest-virtual-machine.md).

    **Considérations**

    * Pour de nombreux déploiements de test, vous pouvez créer un contrôleur de domaine dans Azure. Pour garantir la haute disponibilité du domaine Active Directory, vous pouvez déployer un contrôleur secondaire de domaine supplémentaire.

    * Pour une démonstration simple du déploiement du concept, vous pouvez ignorer cette étape et promouvoir ultérieurement la machine virtuelle du nœud principal comme contrôleur de domaine.

3. Dans le portail Azure Classic ou le portail Azure, créez une machine virtuelle classique en sélectionnant l’image du HPC Pack 2012 R2 dans Azure Marketplace. (Consultez les étapes pour le portail Azure Classic [ici](virtual-machines-windows-tutorial-classic-portal.md).)

    **Considérations**

    * Sélectionnez une taille de machine virtuelle d’au moins A4.

    * Si vous souhaitez déployer le nœud principal dans un réseau virtuel, veillez à spécifier ce réseau dans la configuration de la machine virtuelle.

    * Nous vous recommandons de créer un nouveau service cloud pour la machine virtuelle.

4. Après avoir créé la machine virtuelle et lorsque cette dernière est en cours d’exécution, joignez la machine virtuelle à une forêt de domaines existante ou créez une nouvelle forêt de domaines sur la machine virtuelle.

    **Considérations**

    * Si vous avez créé la machine virtuelle dans un réseau virtuel Azure avec une forêt de domaines existante, connectez-vous à la machine virtuelle. Utilisez ensuite les outils Gestionnaire de serveur ou Windows PowerShell standard pour la joindre à la forêt de domaines. Ensuite, redémarrez.

    * Si la machine virtuelle n’a pas été créée dans un réseau virtuel Azure, ou si elle a été créée dans un réseau virtuel sans forêt de domaines existante, promouvez-la comme contrôleur de domaine. Pour ce faire, connectez-vous à la machine virtuelle, puis utilisez les outils Gestionnaire de serveur ou Windows PowerShell standard. Pour obtenir la procédure détaillée, consultez [Installer une nouvelle forêt Active Directory Windows Server 2012](https://technet.microsoft.com/library/jj574166.aspx).

5. Une fois que la machine virtuelle est en cours d’exécution et jointe à une forêt Active Directory, démarrez les services HPC Pack sur le nœud principal. Pour ce faire :

    a. Connectez-vous à la machine virtuelle à l’aide d’un compte de domaine membre du groupe Administrateurs local. Par exemple, vous pouvez utiliser le compte administrateur que vous avez configuré lorsque vous avez créé la machine virtuelle du nœud principal.

    b. Pour une configuration du nœud principal par défaut, démarrez Windows PowerShell en tant qu’administrateur et tapez la commande suivante :

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    Le démarrage des services HPC Pack peut prendre plusieurs minutes.

    Pour d’autres options de configuration du nœud principal, tapez `get-help HPCHNPrepare.ps1`.


## Étapes suivantes

* Vous pouvez désormais utiliser le nœud principal de votre cluster HPC Windows. Par exemple, vous pouvez démarrer HPC Cluster Manager, ou commencer à travailler avec les applets de commande HPC PowerShell.

* [Ajoutez des machines virtuelles à nœud de calcul](virtual-machines-hpcpack-cluster-node-manage.md) à votre cluster, ou ajoutez [des nœuds d’extension Azure](virtual-machines-hpcpack-cluster-node-burst.md) dans un service cloud.

* Essayez d’exécuter une charge de travail test sur le cluster. Pour obtenir un exemple, consultez le [guide de mise en route](https://technet.microsoft.com/library/jj884144) du HPC Pack.

<!--Image references-->
[headnode]: ./media/virtual-machines-hpcpack-cluster-headnode/headnode.png

<!---HONumber=AcomDC_0204_2016-->