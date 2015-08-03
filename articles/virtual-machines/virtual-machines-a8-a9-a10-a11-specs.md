<properties
 pageTitle="À propos des instances A8, A9, A10 et A11 | Microsoft Azure"
 description="Consultez les informations et les considérations générales sur l’utilisation des instances de calcul intensif Azure A8, A9, A10 et A11."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="07/17/2015"
 ms.author="danlep"/>

# À propos des instances de calcul intensif A8, A9, A10 et A11

Cet article contient des informations générales et des considérations sur l’utilisation des instances Azure A8, A9, A10 et A11, également appelées instances de *calcul intensif*. Les fonctionnalités clés de ces instances sont les suivantes :

* **Matériel hautes performances** : le matériel du centre de données Azure exécutant ces instances a été conçu et optimisé pour les applications nécessitant des ressources réseau et de calcul importantes, notamment les applications en cluster pour du calcul hautes performances, la modélisation et les simulations.

* **Connexion réseau RDMA pour les applications MPI** : si elles sont configurées avec les pilotes réseau nécessaires, les instances A8 et A9 peuvent communiquer avec d’autres instances A8 et A9 sur un réseau haut débit à faible latence dans Azure, basé sur la technologie RDMA (Remote Direct Memory Access). Cette fonctionnalité peut améliorer les performances des applications qui utilisent des implémentations de Linux ou de Windows MPI (Message Passing Interface) prises en charge.

* **Prise en charge des clusters HPC Linux et Windows** : déployez un logiciel de gestion de cluster et de planification des travaux sur les instances A8, A9, A10 et A11 dans Azure pour créer un cluster HPC autonome ou pour ajouter de la capacité à un cluster local. Comme d’autres tailles de machine virtuelle Azure, les instances A8, A9, A10 et A11 prennent en charge les images de système d’exploitation Windows Server et Linux standard ou personnalisées ou les modèles du Gestionnaire des ressources dans les machines virtuelles Azure (IaaS) ou les versions des systèmes d’exploitation invités d’Azure dans les services cloud (PaaS, pour Windows Server uniquement).

>[AZURE.NOTE]Les instances A10 et A11 ont les mêmes optimisations et spécifications en matière de performances que les instances A8 et A9. Cependant, elles n’incluent pas l’accès au réseau RDMA dans Azure. Ces instances sont conçues pour les applications de calcul hautes performances qui n’ont pas besoin d’une communication constante et à latence faible entre les nœuds. Ces applications sont également appelées applications paramétriques ou massivement parallèles. Lors de l’exécution des charges de travail autres que les applications MPI, les instances A8 et A9 n’accèdent pas au réseau RDMA et sont fonctionnellement équivalentes aux instances A10 et A11.


## Spécifications

### Processeur et mémoire

Les instances de calcul intensif Azure A8, A9, A10 et A11 ont pour caractéristiques une vitesse élevée, des processeurs multicœurs et de grandes quantités de mémoire, comme le montre le tableau suivant.

Taille | UC | Mémoire
------------- | ----------- | ----------------
A8 et A10 | Intel® Xeon® E5-2670<br/>8 cœurs @ 2.6 GHz | DDR3-1600 MHz<br/>56 Go
A9 et A11 | Intel® Xeon® E5-2670<br/>16 cœurs @ 2.6 GHz | DDR3-1600 MHz<br/>112 Go


>[AZURE.NOTE]Vous pouvez trouver des informations plus détaillées, y compris les extensions du jeu d’instructions pris en charge, sur le site web Intel.com. Pour plus d’informations sur les capacités de stockage des machines virtuelles et sur les disques, consultez [Tailles des machines virtuelles](virtual-machines-size-specs.md).

### Adaptateurs réseau

Les instances A8 et A9 ont deux cartes réseau, qui se connectent aux réseaux Azure principaux suivants.


Réseau | Description
-------- | -----------
Ethernet 10 Gbits/s | Se connecte aux services Azure (comme le stockage et le réseau virtuel) et à Internet
Principal 32 Gbits/s, compatible RDMA | Permet une communication des applications haut débit à latence faible entre les instances au sein d’un même service cloud ou d’un même groupe à haute disponibilité. Réservé uniquement au trafic MPI.


>[AZURE.IMPORTANT]Sur les machines virtuelles A8 et A9 exécutant Linux dans IaaS, l’accès au réseau RDMA n’est actuellement permis que via les applications qui utilisent Azure Linux RDMA et Intel MPI Library 5.0 sur SUSE Linux Enterprise Server 12. Sur les instances A8 et A9 exécutant Windows Server dans IaaS ou PaaS, l’accès au réseau RDMA n’est actuellement permis que via les applications qui utilisent l’interface Microsoft Network Direct via Microsoft MPI (MS-MPI). Pour connaître les conditions requises supplémentaires, consultez la section [Accès au réseau RDMA](#access-the-rdma-network) dans cet article.

Les instances A10 et A11 ont une seule carte réseau Ethernet 10 Gbits/s qui se connecte aux services Azure et à Internet.

## Considérations pour l’abonnement Azure

* **Compte Azure** : si vous voulez déployer davantage qu’un petit nombre d’instances de calcul intensif, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Vous pouvez également utiliser votre abonnement MSDN. Consultez [Avantages d’Azure pour les abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si vous utilisez une [version d’essai gratuit d’Azure](http://azure.microsoft.com/pricing/free-trial/), vous pouvez utiliser seulement un nombre limité de cœurs de calcul Azure.

* **Quota de cœurs** : il peut être nécessaire d’augmenter le quota de cœurs de votre abonnement Azure, qui est par défaut de 20 cœurs, ce qui est insuffisant pour de nombreux scénarios avec des instances de 8 ou de 16 cœurs. Pour les premiers tests, vous pouvez envisager de demander une augmentation du quota à 100 cœurs. Pour cela, ouvrez un ticket de support gratuit comme indiqué dans [Présentation des limites et des augmentations d’Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

>[AZURE.NOTE]Les quotas d’Azure sont des limites de crédit et non des garanties de capacité. Vous êtes facturé seulement pour les cœurs que vous utilisez.

* **Groupe d’affinités** : pour le moment, les groupes d’affinités ne sont pas recommandés pour la plupart des nouveaux déploiements. Toutefois, notez que si vous utilisez un groupe d’affinités contenant des instances de tailles autres que A8 - A11, vous ne pourrez pas l’utiliser non plus pour les instances A8 à A11, et inversement.

* **Réseau virtuel** : un réseau virtuel Azure n’est pas requis pour utiliser les instances de calcul intensif. Cependant, vous pouvez avoir besoin d’au moins un réseau virtuel Azure dans le cloud pour nombre de scénarios IaaS, ou d’une connexion de site à site si vous devez accéder à des ressources locales, par exemple à un serveur de licences d’application. Vous devrez créer un réseau virtuel (régional) avant de déployer les instances. L’ajout d’une machine virtuelle A8, A9, A10 ou A11 à un réseau virtuel dans un groupe d’affinités n’est pas pris en charge. Pour plus d’informations, consultez [Créer un réseau virtuel](https://msdn.microsoft.com/library/azure/dn631643.aspx) et [Configurer un réseau virtuel avec une connexion VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md).

* **Service cloud ou groupe à haute disponibilité** : pour se connecter via le réseau RDMA, les instances A8 et A9 doivent être déployées dans le même service cloud (pour des scénarios IaaS avec des machines virtuelles Linux ou des machines virtuelles Windows dans la gestion des services Azure ou pour des scénarios PaaS avec Windows Server) ou dans le même groupe à haute disponibilité (pour les machines virtuelles Linux ou Windows dans Azure Resource Manager).

## Considérations sur l’utilisation de HPC Pack

### Considérations relatives à HPC Pack et Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) est une solution gratuite de Microsoft de gestion des tâches et des clusters HPC pour Windows. Depuis HPC Pack 2012 R2 Update 2, HPC Pack prend en charge l’exécution de plusieurs distributions Linux sur les nœuds de calcul déployés dans les machines virtuelles Azure, gérés par un nœud principal Windows Server. La dernière version de HPC Pack vous permet de déployer un cluster Linux pouvant exécuter les applications MPI qui accèdent au réseau RDMA dans Azure. Pour plus d’informations, consultez la [documentation HPC Pack](http://go.microsoft.com/fwlink/?LinkId=617894).

### Considérations relatives à HPC Pack et Windows

HPC Pack n’est pas requis pour utiliser les instances A8, A9, A10 et A11 avec Windows Server, mais c’est un outil recommandé pour créer des clusters HPC Windows dans Azure. Dans le cas des instances A8 et A9, HPC Pack est le moyen le plus efficace d’exécuter des applications Windows MPI qui accèdent au réseau RDMA dans Azure. HPC Pack comprend un environnement d’exécution pour l’implémentation Microsoft de MPI pour Windows.

Pour plus d’informations et pour obtenir les listes de vérification pour déployer et utiliser les instances de calcul intensif dans des scénarios IaaS et PaaS avec HPC Pack sur Windows Server, consultez [Instances de calcul intensif A8 et A9 : Démarrage rapide avec HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).

## Accès au réseau RDMA

### Accès à partir des machines virtuelles Linux  A8 et A9

Au sein d’un même service cloud ou d’un groupe à haute disponibilité, les instances A8 et A9 peuvent accéder au réseau RDMA dans Azure lors de l’exécution d’applications MPI qui utilisent les pilotes Linux RDMA pour communiquer entre les instances. Pour le moment, Azure Linux RDMA est pris en charge uniquement avec [Intel MPI Library 5.0](https://software.intel.com/fr-fr/intel-mpi-library/).

>[AZURE.NOTE]Actuellement, les pilotes Azure Linux RDMA ne sont pas disponibles pour une installation via des extensions de pilote. Ils le sont uniquement en utilisant l’image SLES 12 compatible RDMA dans Azure Marketplace.

Consultez le tableau suivant pour connaître la configuration requise pour que les applications Linux MPI accèdent au réseau RDMA dans les clusters de nœuds de calcul (IaaS). Pour obtenir des informations sur les options de déploiement et les étapes de configuration, consultez la page [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-cluster-rdma.md).

Configuration requise | Machines virtuelles (IaaS)
------------ | -------------
Système d’exploitation | Image SLES 12 (SUSE Linux Enterprise Server 12) HPC à partir d’Azure Marketplace
MPI | Intel MPI Library 5.0

### Accès à partir des instances Windows A8 et A9

Au sein d’un même service cloud ou groupe à haute disponibilité, les instances A8 et A9 peuvent accéder au réseau RDMA dans Azure lors de l’exécution d’applications MPI qui utilisent l’interface Microsoft Network Direct pour communiquer entre les instances. Les instances A10 et A11 n’incluent pas l’accès au réseau RDMA.

Consultez le tableau suivant pour les configurations requises permettant aux applications MPI d’accéder au réseau RDMA dans les déploiements de machines virtuelles (IaaS) et de services cloud (PaaS) des instances A8 et A9. Pour les scénarios de déploiement classiques, consultez [Instances de calcul intensif A8 et A9 : démarrage rapide avec HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).


Configuration requise | Machines virtuelles (IaaS) | Services cloud (PaaS)
---------- | ------------ | -------------
Système d’exploitation | Machines virtuelles Windows Server 2012 R2 ou Windows Server 2012 | Famille de systèmes d’exploitation invités Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2
MPI | MS-MPI 2012 R2 ou ultérieur, autonome ou installé via HPC Pack 2012 R2 ou ultérieur<br/><br/>Intel MPI Library 5.0 | MS-MPI 2012 R2 ou ultérieur, installé via HPC Pack 2012 R2 ou ultérieur<br/><br/>Intel MPI Library 5.0


>[AZURE.NOTE]Pour les scénarios IaaS, l’[extension HpcVmDrivers](https://msdn.microsoft.com/library/azure/dn690126.aspx) doit être ajouté aux machines virtuelles pour installer les pilotes Windows nécessaires à la connectivité RDMA.


## Autres informations à connaître

* Les tailles de machine virtuelle A8 à A11 sont uniquement disponibles dans le niveau de tarification Standard.

* Vous ne pouvez pas redimensionner une machine virtuelle Azure existante à la taille A8, A9, A10 ou A11.

* Les instances A8, A9, A10 et A11 ne peuvent pas actuellement être déployées en utilisant un service cloud qui fait partie d’un groupe d’affinités existant. De même, un groupe d’affinités avec un service cloud contenant des instances A8, A9, A10 et A11 ne peut pas être utilisé pour les déploiements d’autres tailles d’instance. Si vous tentez d’effectuer ces déploiements, vous verrez un message d’erreur similaire à `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`

* Le réseau RDMA dans Azure réserve l’espace d’adressage 172.16.0.0/12. Si vous envisagez d'exécuter des applications MPI sur des instances A8 et A9 déployées dans un réseau virtuel Azure, assurez-vous que l'espace d'adressage du réseau virtuel ne chevauche pas le réseau RDMA.

## Étapes suivantes

* Pour plus d’informations sur la disponibilité et la tarification des instances A8, A9, A10 et A11, consultez [Tarification des machines virtuelles](http://azure.microsoft.com/pricing/details/virtual-machines/) et [Tarification des services cloud](http://azure.microsoft.com/pricing/details/cloud-services/).
* Pour déployer et configurer un cluster Linux avec des instances A8 et A9 pour accéder au réseau Azure RDMA, consultez la page [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-cluster-rdma.md).
* Pour prendre en main le déploiement et l’utilisation d’instances A8 et A9 avec HPC Pack sur Windows, consultez les pages [Instances de calcul intensif A8 et A9 : démarrage rapide avec HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx) et [Exécuter des applications MPI sur des instances de calcul intensif A8 et A9](https://msdn.microsoft.com/library/azure/dn592104.aspx).

<!---HONumber=July15_HO4-->