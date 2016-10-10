<properties
 pageTitle="À propos des machines virtuelles nécessitant beaucoup de ressources système avec Windows | Microsoft Azure"
 description="Consultez les informations et les considérations générales relatives à l’utilisation de la série H Azure et des tailles Azure nécessitant beaucoup de ressources système A8, A9, A10 et A11 pour des machines virtuelles et services cloud Windows."
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
 ms.date="09/21/2016"
 ms.author="danlep"/>

# À propos des machines virtuelles de série H et de série A nécessitant beaucoup de ressources système

Cet article fournit des informations et quelques considérations générales sur l’utilisation de la nouvelle série H Azure et des instances A8, A9, A10 et A11 antérieures, également appelées instances *nécessitant beaucoup de ressources système*. Cet article se concentre sur l’utilisation de ces instances pour les machines virtuelles Windows. Il est également disponible pour les [machines virtuelles Linux](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accès au réseau RDMA

Vous pouvez créer des clusters d’instances Windows Server prenant en charge RDMA et déployer une des implémentations MPI prises en charge pour tirer parti du réseau RDMA Azure. Ce réseau à faible latence et débit élevé est réservé uniquement au trafic MPI.

* **Système d’exploitation**
    * **Machines virtuelles** : Windows Server 2012 R2, Windows Server 2012
    * **Services cloud** : famille de systèmes d’exploitation invités Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2

* **MPI** : Microsoft MPI (MS-MPI) 2012 R2 ou version ultérieure, bibliothèque Intel MPI 5.x

Les implémentations MPI prises en charge utilisent l’interface Microsoft Network Direct pour la communication entre les instances. Pour des options de déploiement et des exemples de configuration, consultez [Configuration d’un cluster Windows RDMA avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) et [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../batch/batch-mpi.md).


>[AZURE.NOTE]Sur des machines virtuelles prenant en charge RDMA, l’extension HpcVmDrivers doit être ajoutée aux machines virtuelles pour installer les pilotes d’appareils réseau Windows nécessaires à la connectivité RDMA. Dans la plupart des déploiements, l’extension HpcVmDrivers est ajoutée automatiquement. Si vous devez ajouter vous-même l’extension, consultez [Gestion des extensions de machine virtuelle](virtual-machines-windows-classic-manage-extensions.md).

## Considérations relatives à HPC Pack et Windows

[Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solution gratuite de cluster et de gestion des travaux HPC de Microsoft, n’est pas requis pour utiliser les instances nécessitant beaucoup de ressources système avec Windows Server. Il s’agit cependant d’une option permettant de créer un cluster de calcul dans Azure pour exécuter des applications MPI basées sur Windows et d’autres charges de travail HPC. HPC Pack 2012 R2 et les versions ultérieures incluent un environnement d’exécution pour MS-MPI, qui peut utiliser le réseau RDMA Azure en cas de déploiement sur des machines virtuelles prenant en charge RDMA.

Pour plus d'informations et pour obtenir les listes de vérification afin d'utiliser les instances de calcul intensif avec HPC Pack sur Windows Server, consultez [Configuration d'un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## Étapes suivantes

* Pour plus d’informations sur la disponibilité et la tarification des tailles nécessitant beaucoup de ressources système, consultez [Tarification des machines virtuelles Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) et [Tarification Services cloud](https://azure.microsoft.com/pricing/details/cloud-services/).

* Pour plus d’informations sur les capacités de stockage et sur les disques, voir [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).

* Pour commencer à déployer et à utiliser des instances nécessitant beaucoup de ressources système avec HPC Pack sur Windows, voir [Configuration d’un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Pour plus d’informations sur l’utilisation d’instances A8 et A9 afin d’exécuter des applications MPI avec Azure Batch, voir [Utiliser les tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0928_2016-->