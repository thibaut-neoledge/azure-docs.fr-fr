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
 ms.date="05/09/2016"
 ms.author="danlep"/>

# À propos des instances de calcul intensif A8, A9, A10 et A11 

Voici des informations et quelques considérations générales sur l’utilisation des instances Azure A8, A9, A10 et A11, également appelées instances de *calcul intensif*. Cet article se concentre sur l’utilisation de ces instances pour les machines virtuelles Linux. Cet article est également disponible pour les [machines virtuelles Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accès au réseau RDMA

Au sein d’un même service Cloud ou groupe à haute disponibilité, les clusters de machines virtuelles Linux de taille A8 et A9 qui exécutent une des distributions HPC Linux prises en charge suivantes et une implémentation MPI prise en charge peuvent accéder au réseau RDMA dans Azure pour exécuter des applications MPI Linux. Pour obtenir des informations sur les options de déploiement et des exemples d’étapes de configuration, consultez la page [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).

* **Distributions** -SUSE Linux Enterprise Server (SLES) 12 pour HPC, SLES 12 pour HPC (Premium), HPC basé sur CentOS 7.1 ou sur CentOS 6.5, déployé à partir d’une image Azure Marketplace

* **MPI** - Intel MPI Library 5.x

    >[AZURE.NOTE] Intel MPI 5.1.3.181 est déjà installé sur les images HPC basée sur CentOS dans Marketplace. Pour utiliser Intel MPI sur des machines virtuelles HPC SLES 12, vous devez l’installer séparément.

Actuellement, les pilotes Azure Linux RDMA sont installés uniquement lorsque vous déployez des images HPC SLES 12 et HPC CentOS compatibles RDMA provenant d’Azure Marketplace. Vous ne pouvez pas installer les pilotes sur les autres machines virtuelles Linux que vous déployez.

>[AZURE.NOTE]Sur les images HPC basées sur CentOS provenant du Marketplace, les mises à jour du noyau sont désactivées dans le fichier de configuration **yum**. Cela s’explique par le fait que les pilotes RDMA Linux sont distribués sous forme de package RPM, et que les mises à jour du pilote peuvent ne pas fonctionner si le noyau est mis à jour.


## Mises à jour de pilote RDMA pour SLES 12
Après avoir créé une machine virtuelle basée sur une image SLES 12 HPC, vous devez mettre à jour les pilotes RDMA sur les machines virtuelles pour assurer la connectivité réseau RDMA.

>[AZURE.IMPORTANT]Cette étape est **requise** pour les déploiements de machines virtuelles HPC SLES 12 dans toutes les régions Azure. Cette étape n’est pas nécessaire si vous avez déployé une machine virtuelle basée sur CentOS HPC 7.1 ou 6.5.

Avant de mettre à jour les pilotes, arrêtez tous les processus **zypper** ou tous les processus qui verrouillent les bases de données de référentiel SUSE sur la machine virtuelle. Sinon, la mise à jour des pilotes risque de ne pas se dérouler correctement.

Pour mettre à jour les pilotes RDMA Linux sur chaque machine virtuelle, exécutez sur votre ordinateur client l’un des ensembles suivants de commandes de l’interface de ligne de commande Azure.

**Pour une machine virtuelle HPC SLES 12 configurée dans le modèle de déploiement classique**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Pour une machine virtuelle HPC SLES 12 configurée dans le modèle de déploiement Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]L’installation des pilotes peut prendre un certain temps, et la commande ne retourne aucune sortie. Après la mise à jour, votre machine virtuelle redémarre et doit être opérationnelle en quelques minutes.

### Exemple de script pour les mises à jour de pilote

Si vous avez un cluster de machines virtuelles HPC SLES 12, vous pouvez exécuter la mise à jour des pilotes sur tous les nœuds de votre cluster à l’aide d’un script. Par exemple, le script suivant met à jour les pilotes dans un cluster à 8 nœuds.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## Considérations relatives à HPC Pack et Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) est une solution gratuite de Microsoft de gestion des tâches et des clusters HPC. Les dernières versions de HPC Pack 2012 R2 prennent en charge l’exécution de plusieurs distributions Linux sur les nœuds de calcul déployés dans les machines virtuelles Azure et gérés par un nœud principal Windows Server. Les nœuds de calcul Linux déployés sur des machines virtuelles A8 ou A9 et exécutant une implémentation MPI prise en charge peuvent exécuter des applications MPI qui accèdent au réseau RDMA. Pour commencer, consultez [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Considérations sur la topologie réseau

* Sur les machines virtuelles Linux de taille A8 ou A9 dans Azure, Eth1 est réservé au trafic réseau RDMA. Ne modifiez aucun paramètre Eth1 ou des informations du fichier de configuration faisant référence à ce réseau. Eth0 est réservé au trafic réseau Azure normal.

* Dans Azure, IP over Infiniband (IB) n'est pas pris en charge. Seul RDMA over IB est pris en charge.


## Étapes suivantes

* Pour plus d’informations sur la disponibilité et la tarification des instances A8, A9, A10 et A11, consultez [Tarification de Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Pour plus d’informations sur les capacités de stockage et sur les disques, voir [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).

* Pour commencer à déployer et à utiliser des instances A8 et A9 avec RDMA sur Linux, consultez [Configuration d’un cluster RDMA Linux pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0511_2016-->