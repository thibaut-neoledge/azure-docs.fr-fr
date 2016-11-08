---
title: À propos des machines virtuelles nécessitant beaucoup de ressources système avec Linux | Microsoft Docs
description: Obtenez des informations et considérations générales sur l’utilisation de la série H et des tailles A8, A9, A10 et A11 nécessitant beaucoup de ressources système pour les machines virtuelles Linux
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: danlep

---
# À propos des machines virtuelles de série H et de série A nécessitant beaucoup de ressources système
Cet article fournit des informations et quelques considérations générales sur l’utilisation de la nouvelle série H Azure et des tailles A8, A9, A10 et A11 antérieures, également appelées instances *nécessitant beaucoup de ressources système*. Cet article se concentre sur l’utilisation de ces tailles pour les machines virtuelles Linux. Cet article est également disponible pour les [machines virtuelles Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Accès au réseau RDMA
Vous pouvez créer des clusters de machines virtuelles Linux prenant en charge RDMA qui exécutent l’une des distributions Linux HPC prises en charge suivantes, et une implémentation MPI prise en charge pour tirer parti du réseau RDMA Azure. Pour obtenir des informations sur les options de déploiement et des exemples d’étapes de configuration, consultez la page [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).

* **Distributions** : vous devez déployer des machines virtuelles à partir d’images SUSE Linux Enterprise Server (SLES) prenant en charge RDMA ou d’images HPC basées sur OpenLogic CentOS dans la Place de marché Azure. Seules les images suivantes de la Place de marché prennent en charge les pilotes RDMA Linux nécessaires :
  
  * SLES 12 SP1 pour HPC, SLES 12 SP1 pour HPC (Premium)
  * SLES 12 pour HPC, SLES 12 pour HPC (Premium)
  * HPC basé sur CentOS 7.1
  * HPC basé sur CentOS 6.5
    
    > [!NOTE]
    > Pour les machines virtuelles de la série H, nous vous recommandons une image SLES 12 SP1 pour HPC ou une image HPC basée sur CentOS 7.1.
    > 
    > Sur les images HPC basées sur CentOS, les mises à jour du noyau sont désactivées dans le fichier de configuration **yum**. Cela s’explique par le fait que les pilotes RDMA Linux sont distribués sous forme de package RPM, et que les mises à jour du pilote peuvent ne pas fonctionner si le noyau est mis à jour.
    > 
    > 
* **MPI** - Intel MPI Library 5.x
  
    En fonction de l’image de la Place de marché choisie, une gestion de licence, une installation ou une configuration distinctes d’Intel MPI peuvent être nécessaires, comme suit :
  
  * **Image SLES 12 SP1 pour HPC** : installez les packages Intel MPI distribués sur la machine virtuelle en exécutant la commande suivante :
    
          sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  * **Image SLES 12 pour HPC** : vous devez vous inscrire séparément pour télécharger et installer Intel MPI. Voir le [Guide d’installation de la bibliothèque Intel MPI](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf).
  * **Images HPC basées sur CentOS** : Intel MPI 5.1 est déjà installé.
    
    Une configuration supplémentaire du système est nécessaire pour exécuter des travaux MPI sur des machines virtuelles en cluster. Par exemple, sur un cluster de machines virtuelles, vous devez établir une approbation entre les nœuds de calcul. Pour les paramètres classiques, consultez [Configurer un cluster RDMA Linux pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).

## Considérations relatives à HPC Pack et Linux
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), solution gratuite de cluster HPC et de gestion des travaux de Microsoft, fournit une option pour utiliser les instances nécessitant beaucoup de ressources système avec Linux. Les dernières versions de HPC Pack 2012 R2 prennent en charge l’exécution de plusieurs distributions Linux sur les nœuds de calcul déployés dans les machines virtuelles Azure et gérés par un nœud principal Windows Server. Avec des nœuds de calcul Linux prenant en charge RDMA exécutant Intel MPI, HPC Pack peut planifier et exécuter des applications MPI Linux qui accèdent au réseau RDMA. Pour commencer, consultez [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Considérations sur la topologie réseau
* Sur des machines virtuelles Linux compatibles RDMA dans Azure, Eth1 est réservé au trafic réseau RDMA. Ne modifiez aucun paramètre Eth1 ou des informations du fichier de configuration faisant référence à ce réseau. Eth0 est réservé au trafic réseau Azure normal.
* Dans Azure, IP over Infiniband (IB) n’est pas pris en charge. Seul RDMA over IB est pris en charge.

## Mises à jour de pilote RDMA pour SLES 12
Après avoir créé une machine virtuelle basée sur une image SLES 12 HPC, il se peut que vous deviez mettre à jour les pilotes RDMA sur les machines virtuelles pour assurer la connectivité réseau RDMA.

> [!IMPORTANT]
> Cette étape est **requise** pour les déploiements de machines virtuelles SLES 12 pour HPC dans toutes les régions Azure. Cette étape n’est pas nécessaire si vous déployez une machine virtuelle SLES 12 SP1 pour HPC, une image HPC basée sur CentOS 7.1 ou une image HPC basée sur CentOS 6.5.
> 
> 

Avant de mettre à jour les pilotes, arrêtez tous les processus **zypper** ou tous les processus qui verrouillent les bases de données de référentiel SUSE sur la machine virtuelle. Sinon, la mise à jour des pilotes risque de ne pas se dérouler correctement.

Pour mettre à jour les pilotes RDMA Linux sur chaque machine virtuelle, exécutez sur votre ordinateur client l’un des ensembles suivants de commandes de l’interface de ligne de commande Azure.

**Machine virtuelle SLES 12 pour HPC configurée dans le modèle de déploiement classique**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Machine virtuelle SLES 12 pour HPC configurée dans le modèle de déploiement Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

> [!NOTE]
> L’installation des pilotes peut prendre un certain temps et la commande ne retourne aucune sortie. Après la mise à jour, votre machine virtuelle redémarre et doit être opérationnelle en quelques minutes.
> 
> 

### Exemple de script pour les mises à jour de pilote
Si vous avez un cluster de machines virtuelles SLES 12 pour HPC, vous pouvez exécuter la mise à jour du pilote sur tous les nœuds de votre cluster à l’aide d’un script. Par exemple, le script suivant met à jour les pilotes dans un cluster à 8 nœuds.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## Étapes suivantes
* Pour plus d’informations sur la disponibilité et la tarification des tailles nécessitant beaucoup de ressources système, consultez [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).
* Pour plus d’informations sur les capacités de stockage et sur les disques, voir [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).
* Pour commencer à déployer et à utiliser des tailles nécessitant beaucoup de ressources système avec RDMA sur Linux, consultez [Configurer un cluster RDMA Linux pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0928_2016-->