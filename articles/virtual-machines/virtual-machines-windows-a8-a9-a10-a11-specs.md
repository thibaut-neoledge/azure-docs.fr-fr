<properties
 pageTitle="À propos des instances A8 à A11 avec Windows | Microsoft Azure"
 description="Consultez les informations et les considérations générales relatives à l’utilisation d’instances de calcul intensif Azure A8, A9, A10 et A11 pour les machines virtuelles Windows et les services cloud."
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="04/26/2016"
 ms.author="danlep"/>

# À propos des instances de calcul intensif A8, A9, A10 et A11

Cet article fournit des informations et quelques considérations générales sur l’utilisation des instances Azure A8, A9, A10 et A11, également appelées instances *nécessitant beaucoup de ressources système*. Cet article se concentre sur l’utilisation de ces instances pour les machines virtuelles Windows. Il est également disponible pour les [machines virtuelles Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accès au réseau RDMA

Au sein d’un même service cloud, groupe à haute disponibilité ou pool Azure Batch, les instances A8 et A9 peuvent accéder au réseau RDMA dans Azure pour l’exécution d’applications MPI Windows qui utilisent l’interface Microsoft Network Direct pour communiquer entre les instances.

Consultez le tableau suivant pour découvrir les configurations requises permettant aux applications MPI d’accéder au réseau RDMA dans les machines virtuelles Windows, les services cloud et les pools Azure Batch des instances A8 et A9. Pour les scénarios de déploiement types, voir [Configuration d’un cluster Windows RDMA avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) et [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../batch/batch-mpi.md).


Configuration requise | Machines virtuelles | Services cloud ou pool Batch 
---------- | ------------ | ------------- 
Système d’exploitation | Windows Server 2012 R2 ou Windows Server 2012 | Famille de systèmes d’exploitation invités Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 
MPI | MS-MPI 2012 R2 ou version ultérieure, ou Intel MPI Library 5 | MS-MPI 2012 R2 ou version ultérieure, ou Intel MPI Library 5 


>[AZURE.NOTE]Pour les machines virtuelles de taille A8 et A9, l’extension HpcVmDrivers doit être ajoutée aux machines virtuelles pour installer les pilotes d’appareils réseau Windows nécessaires à la connectivité RDMA. Selon votre méthode de déploiement, l’extension HpcVmDrivers peut être ajoutée automatiquement à une machine virtuelle de taille A8 ou A9, ou vous devrez peut-être l’ajouter vous-même. Pour ajouter l’extension, voir [Gestion des extensions de machine virtuelle](virtual-machines-windows-classic-manage-extensions.md).

## Considérations relatives à HPC Pack et Windows

HPC Pack n’est pas requis pour utiliser les instances A8, A9, A10 et A11 avec Windows Server, mais cet outil peut se révéler utile pour exécuter les applications MPI Windows qui accèdent au réseau RDMA dans Azure. HPC Pack 2012 R2 et les versions ultérieures intègrent un environnement d’exécution de MS-MPI (implémentation Microsoft de Message Passing Interface) pour Windows qui peut utiliser le réseau RDMA Azure lors du déploiement sur les instances A8 et A9.

Pour plus d'informations et pour obtenir les listes de vérification afin d'utiliser les instances de calcul intensif avec HPC Pack sur Windows Server, consultez [Configuration d'un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## Étapes suivantes

* Pour plus d'informations sur la disponibilité et la tarification des instances A8, A9, A10 et A11, consultez [Tarification de Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) et [Tarification de Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/).

* Pour plus d’informations sur les capacités de stockage et sur les disques, voir [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).

* Pour commencer à déployer et à utiliser des instances A8 et A9 avec HPC Pack sur Windows, consultez [Configuration d'un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Pour plus d’informations sur l’utilisation d’instances A8 et A9 afin d’exécuter des applications MPI avec Azure Batch, voir [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0511_2016-->