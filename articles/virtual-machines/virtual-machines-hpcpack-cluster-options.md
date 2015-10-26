<properties
 pageTitle="Options de cluster HPC Pack dans le cloud | Microsoft Azure"
 description="Découvrez les options de Microsoft HPC Pack pour créer et gérer un cluster HPC (High Performance Computing) dans le cloud Azure."
 services="virtual-machines,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/08/2015"
 ms.author="danlep"/>

# Options pour créer et gérer un cluster HPC (High Performance Computing) dans Azure avec Microsoft HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Tirez parti de Microsoft HPC Pack et des services de calcul et d’infrastructure Azure pour créer et gérer un cluster HPC sur le cloud. [HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) est la solution HPC gratuite de Microsoft construite autour des technologies Microsoft Azure et Windows Server, prenant en charge des charges de travail HPC Windows et Linux. Un cluster HPC Pack cloud fournit à un administrateur de cluster ou un éditeur de logiciels indépendant (ISV) une plateforme flexible et évolutive pour exécuter des applications exigeantes tout en réduisant l’investissement dans une infrastructure de cluster de calcul locale.

<!-- Take advantage of automated tools for HPC Pack cluster deployment in Azure VMs, including an HPC Pack image that can be used with either Azure quickstart templates or Azure PowerShell scripts, or deploy your cluster manually in the Azure portal.-->


## Configuration d’un cluster HPC Pack dans Azure


### Images d’ordinateur virtuel Azure

* [HPC Pack sur Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nœud de calcul de HPC Pack sur Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nœud de calcul de HPC Pack avec Excel sur Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### Modèles de démarrage rapide Microsoft Azure

* [Création d’un cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* [Création d'un cluster HPC avec des nœuds de calcul Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

* [Création d’un cluster HPC avec une image de nœud de calcul personnalisée](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Script de déploiement PowerShell

* [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md)

### Didacticiels

* [Didacticiel : prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack.md)

* [Didacticiel : exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack-namd.md)

* [Didacticiel : prise en main d’un cluster HPC Pack dans Azure pour exécuter des charges de travail Excel et SOA](../virtual-machines/virtual-machines-excel-cluster-hpcpack.md)



### Déploiement manuel avec le portail Azure



* [Configurer le nœud principal d’un cluster HPC Pack dans une machine virtuelle Azure](virtual-machines-hpcpack-cluster-headnode.md)

### Gestion de cluster

* [Gérer des nœuds de calcul dans un cluster HPC Pack dans Azure](virtual-machines-hpcpack-cluster-node-manage.md)

* [Ajouter des nœuds « en rafale » Azure à un nœud principal HPC Pack dans Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Agrandir et réduire les ressources de calcul Azure dans un cluster HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [Envoyer des travaux à un cluster HPC Pack dans Azure](virtual-machines-hpcpack-cluster-submit-jobs.md)



## Ajouter des nœuds de rôle de travail à un cluster HPC Pack


* [Intégration à Azure avec HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Didacticiel : configurer un cluster hybride avec HPC Pack dans Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Ajouter des nœuds « en rafale » Azure à un nœud principal HPC Pack dans Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Agrandir et réduire les ressources de calcul Azure dans un cluster HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)


## Créer des clusters RDMA pour des charges de travail MPI

* [Configuration d’un cluster Windows RDMA avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-hpcpack-cluster-rdma.md)

<!-- * [Set up a Linux RDMA cluster to run MPI applications](virtual-machines-linux-hpcpack-cluster-rdma.md) -->

<!---HONumber=Oct15_HO3-->