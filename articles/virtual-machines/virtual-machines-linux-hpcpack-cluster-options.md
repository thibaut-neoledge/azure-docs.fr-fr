<properties
 pageTitle="Options de cluster HPC Pack Linux dans le cloud | Microsoft Azure"
 description="Découvrez les options de Microsoft HPC Pack pour créer et gérer un cluster HPC (High Performance Computing) Linux dans le cloud Azure"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/29/2016"
 ms.author="danlep"/>

# Options pour créer et gérer un cluster HPC (calcul haute performance) dans Azure avec Microsoft HPC Pack

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Cet article traite des options d’utilisation de HPC Pack pour exécuter des charges de travail Linux. Il existe également des options d’exécution [de charges de travail Windows HPC avec HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Configuration d’un cluster HPC Pack dans Azure

### Modèles Azure


* (Marketplace) [Cluster HPC Pack pour les charges de travail Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Démarrage rapide) [Création d’un cluster HPC avec des nœuds de calcul Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)


### Images d’ordinateur virtuel Azure

* [HPC Pack sur Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)



### Script de déploiement PowerShell

* [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Didacticiels

* [Didacticiel : prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Didacticiel : exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Didacticiel : Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Didacticiel : Exécuter STAR-CCM+ avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### Gestion de cluster

* [Envoyer des travaux à un cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)


## Créer des clusters RDMA pour des charges de travail MPI

* [Didacticiel : Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0504_2016-->