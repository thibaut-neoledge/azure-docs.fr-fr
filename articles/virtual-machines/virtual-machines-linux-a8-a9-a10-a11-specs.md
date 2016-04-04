<properties
 pageTitle="À propos des instances Linux A8 à A11 | Microsoft Azure"
 description="Consultez les informations et les considérations générales sur l’utilisation des instances de calcul intensif Azure A8, A9, A10 et A11 pour les machines virtuelles Linux."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# À propos de l’utilisation des instances de calcul intensif A8, A9, A10 et A11 avec Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accès au réseau RDMA

Au sein d’un même service cloud ou d’un groupe à haute disponibilité, les instances A8 et A9 peuvent accéder au réseau RDMA dans Azure pour exécuter des applications MPI Linux. Pour le moment, Azure Linux RDMA est pris en charge uniquement avec [Intel MPI Library 5](https://software.intel.com/fr-FR/intel-mpi-library/).

>[AZURE.NOTE] Actuellement, les pilotes Azure Linux RDMA ne sont pas disponibles pour une installation via des extensions de pilote. Ils le sont uniquement en utilisant l’image SLES 12 compatible RDMA dans Azure Marketplace.

Le tableau suivant résume la configuration requise pour que les applications MPI Linux accèdent au réseau RDMA dans les clusters de nœuds de calcul (IaaS). Pour obtenir des informations sur les options de déploiement et les étapes de configuration, consultez la page [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).

Configuration requise | Machines virtuelles (IaaS)
------------ | -------------
Système d’exploitation | Image SLES 12 HPC dans Azure Marketplace
MPI | Intel MPI Library 5

## Considérations relatives à HPC Pack et Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) est une solution gratuite de Microsoft de gestion des tâches et des clusters HPC pour Windows. Les dernières versions de HPC Pack 2012 R2 prennent en charge l’exécution de plusieurs distributions Linux sur les nœuds de calcul déployés dans les machines virtuelles Azure et gérés par un nœud principal Windows Server. Les nœuds de calcul Linux déployés sur des machines virtuelles A8 ou A9 et exécutant une implémentation MPI prise en charge peuvent exécuter des applications MPI qui accèdent au réseau RDMA. Pour commencer, consultez [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Étapes suivantes

* Pour plus d’informations sur la disponibilité et la tarification des instances A8, A9, A10 et A11, consultez [Tarification de Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).

* Pour commencer à déployer et à utiliser des instances A8 et A9 avec RDMA sur Linux, consultez [Configuration d’un cluster RDMA Linux pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0323_2016-->