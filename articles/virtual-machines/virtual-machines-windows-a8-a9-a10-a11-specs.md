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
 ms.date="01/26/2016"
 ms.author="danlep"/>

# À propos de l’utilisation des instances de calcul intensif A8, A9, A10 et A11 avec Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accès au réseau RDMA

Au sein d’un même service cloud ou groupe à haute disponibilité, les instances A8 et A9 peuvent accéder au réseau RDMA dans Azure pour l’exécution d’applications MPI Windows qui utilisent l’interface Microsoft Network Direct pour communiquer entre les instances.

Consultez le tableau suivant pour les configurations requises permettant aux applications MPI d’accéder au réseau RDMA dans les déploiements de machines virtuelles (IaaS) et de services cloud (PaaS) des instances A8 et A9. Pour les scénarios de déploiement classiques, consultez [Configuration d'un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).


Configuration requise | Machines virtuelles (IaaS) | Services cloud (PaaS)
---------- | ------------ | -------------
Système d’exploitation | Windows Server 2012 R2 ou Windows Server 2012 | Famille de systèmes d’exploitation invités Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2
MPI | MS-MPI 2012 R2 ou ultérieur, autonome ou installé via HPC Pack 2012 R2 ou ultérieur<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 ou ultérieur, installé via HPC Pack 2012 R2 ou ultérieur<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]Pour les scénarios IaaS, l'extension HpcVmDrivers doit être ajoutée aux machines virtuelles pour installer les pilotes d'appareils réseau Windows nécessaires à la connectivité RDMA. Selon votre méthode de déploiement, l'extension HpcVmDrivers peut être ajoutée automatiquement à une machine virtuelle de taille A8 ou A9, ou vous devrez peut-être l'ajouter vous-même. Pour ajouter l'extension, consultez [Gestion des extensions de machine virtuelle](virtual-machines-windows-classic-manage-extensions.md).

## Considérations relatives à HPC Pack et Windows

HPC Pack n’est pas requis pour utiliser les instances A8, A9, A10 et A11 avec Windows Server, mais c’est un outil recommandé pour exécuter les applications MPI Windows qui accèdent au réseau RDMA dans Azure. HPC Pack comprend un environnement d’exécution pour l’implémentation Microsoft de MPI (Message Passing Interface) (MS-MPI) pour Windows.

Pour plus d'informations et pour obtenir les listes de vérification afin d'utiliser les instances de calcul intensif avec HPC Pack sur Windows Server, consultez [Configuration d'un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

## Étapes suivantes

* Pour plus d'informations sur la disponibilité et la tarification des instances A8, A9, A10 et A11, consultez [Tarification de Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) et [Tarification de Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/).

* Pour commencer à déployer et à utiliser des instances A8 et A9 avec HPC Pack sur Windows, consultez [Configuration d'un cluster RDMA Windows avec HPC Pack pour exécuter des applications MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Pour plus d’informations sur l’utilisation d’instances A8 et A9 afin d’exécuter des applications MPI avec Azure Batch, consultez [Utiliser des tâches multi-instances pour exécuter des applications MPI (Message Passing Interface) dans Azure Batch](../batch/batch-mpi.md).

<!---HONumber=AcomDC_0323_2016-->