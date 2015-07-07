<properties
 pageTitle="À propos des instances A8, A9, A10 et A11 | Microsoft Azure"
 description="Cet article contient des informations et des considérations générales sur l’utilisation des instances de calcul intensif A8, A9, A10 et A11 Azure."
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
 ms.date="06/09/2015"
 ms.author="danlep"/>

# À propos des instances de calcul intensif A8, A9, A10 et A11

Cet article contient des informations générales et des considérations sur l’utilisation des instances Azure A8, A9, A10 et A11, également appelées instances de *calcul intensif*. Les fonctionnalités clés qui distinguent ces instances sont les suivantes :

* **Matériel hautes performances** : le matériel du centre de données Azure exécutant ces instances a été conçu et optimisé pour les applications nécessitant des ressources réseau et de calcul importantes, notamment les applications en cluster pour du calcul hautes performances, la modélisation et les simulations.

* **Connexion réseau RDMA** : les instances A8 et A9 peuvent communiquer sur un réseau haut débit à faible latence dans Azure, basé sur la technologie RDMA (Remote Direct Memory Access). Cette fonctionnalité peut améliorer les performances pour les applications MPI (Message Passing Interface). (L’accès réseau RDMA est actuellement pris en charge seulement pour les services cloud et les machines virtuelles basées sur Windows Server.)

>[AZURE.NOTE]Les instances A10 et A11 ont les mêmes optimisations et spécifications en matière de performances que les instances A8 et A9. Cependant, elles n’incluent pas l’accès au réseau RDMA dans Azure. Ces instances sont conçues pour les applications de calcul hautes performances qui n’ont pas besoin d’une communication constante et à latence faible entre les nœuds. Ces applications sont également appelées applications paramétriques ou massivement parallèles.

Comme d’[autres tailles de machine virtuelle Azure](virtual-machines-size-specs.md), les instances A8, A9, A10 et A11 prennent en charge les images de système d’exploitation Windows Server et Linux standard ou personnalisées dans les machines virtuelles Azure (IaaS) ou les éditions des systèmes d’exploitation invités d’Azure dans les services cloud (PaaS).

## Spécifications

### Processeur et mémoire

Les instances de calcul intensif Azure A8, A9, A10 et A11 ont pour caractéristiques une vitesse élevée, des processeurs multicœurs et de grandes quantités de mémoire, comme le montre le tableau suivant.

Taille | UC | Mémoire
------------- | ----------- | ----------------
A8 et A10 | Intel® Xeon® E5-2670<br/>8 cœurs @ 2.6 GHz | DDR3-1600 MHz<br/>56 Go
A9 et A11 | Intel® Xeon® E5-2670<br/>16 cœurs @ 2.6 GHz | DDR3-1600 MHz<br/>112 Go


>[AZURE.NOTE]Vous pouvez trouver des informations plus détaillées, y compris les extensions du jeu d’instructions pris en charge, sur le site web Intel.com.


### Adaptateurs réseau

Les instances A8 et A9 ont deux cartes réseau, qui se connectent aux réseaux Azure principaux suivants.


Réseau | Description
-------- | -----------
Ethernet 10 Gbits/s | Se connecte aux services Azure (comme le stockage et le réseau virtuel) et à Internet
Principal 32 Gbits/s, compatible RDMA | Permet une communication des applications haut débit à latence faible entre les instances au sein d’un même service cloud


>[AZURE.IMPORTANT]L’accès au réseau RDMA est actuellement activé seulement via des applications qui utilisent l’interface Microsoft Network Direct. Consultez [Accéder au réseau RDMA](#access-the-RDMA-network) dans cet article.


Les instances A10 et A11 ont une seule carte réseau Ethernet 10 Gbits/s qui se connecte aux services Azure et à Internet.

## Considérations pour l’abonnement Azure

* **Compte Azure** : si vous voulez déployer davantage qu’un petit nombre d’instances de calcul intensif, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Vous pouvez également utiliser votre abonnement MSDN. Consultez [Avantages d’Azure pour les abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si vous utilisez une [version d’essai gratuit d’Azure](http://azure.microsoft.com/pricing/free-trial/), vous pouvez utiliser seulement un nombre limité de cœurs de calcul Azure.

* **Quota de cœurs** : il peut être nécessaire d’augmenter le quota de cœurs de votre abonnement Azure, qui est par défaut de 20 cœurs, ce qui est insuffisant pour de nombreux scénarios avec des instances de 8 ou de 16 cœurs. Pour les premiers tests, vous pouvez envisager de demander une augmentation du quota à 100 cœurs. Pour cela, ouvrez un ticket de support gratuit comme indiqué dans [Présentation des limites et des augmentations d’Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

>[AZURE.NOTE]Les quotas d’Azure sont des limites de crédit et non pas des garanties de capacité. Vous êtes facturé seulement pour les cœurs que vous utilisez.

* **Groupe d’affinités** : un groupe d’affinités Azure peut aider à optimiser les performances en regroupant des services ou des machines virtuelles dans le même centre de données Azure. Pour regrouper les instances de calcul intensif, nous vous recommandons de créer un groupe d’affinités dans une région où vous prévoyez de déployer les instances. Au titre de bonne pratique, utilisez le groupe d’affinités seulement pour les instances de calcul intensif, et non pas pour les instances d’autres tailles.

* **Réseau virtuel** : un réseau virtuel Azure n’est pas requis pour utiliser les instances de calcul intensif. Cependant, vous pouvez avoir besoin d’au moins un réseau virtuel Azure dans le cloud pour beaucoup de scénarios IaaS, ou d’une connexion de site à site si vous devez accéder à des ressources locales. Vous devrez créer un réseau virtuel (régional) avant de déployer les instances. L’ajout d’une machine virtuelle A8, A9, A10 ou A11 à un réseau virtuel dans un groupe d’affinités n’est pas pris en charge. Pour plus d’informations, consultez [Créer un réseau virtuel](https://msdn.microsoft.com/library/azure/dn631643.aspx) et [Configurer un réseau virtuel avec une connexion VPN de site à site](../vpn-gateway/vpn-gateway-site-to-site-create.md).

## Considérations sur l’utilisation de HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029), la solution gratuite de gestion des clusters et des tâches HPC de Microsoft, n’est pas requis pour utiliser les instances A8, A9, A10 et A11, mais c’est un outil recommandé pour créer des clusters Windows HPC dans Azure. Dans le cas des instances A8 et A9, HPC Pack est le moyen le plus efficace d’exécuter des applications Windows MPI qui accèdent au réseau RDMA dans Azure. HPC Pack comprend un environnement d’exécution pour l’implémentation Microsoft de MPI (Message Passing Interface) (MS-MPI) pour Windows.

Pour plus d’informations et des listes de contrôle pour utiliser les instances de calcul intensif avec HPC Pack, consultez [Instances de calcul intensif A8 et A9 : démarrage rapide avec HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).

## Accès au réseau RDMA

Au sein d’un même service cloud, les instances A8 et A9 peuvent accéder au réseau RDMA dans Azure lors de l’exécution d’applications MPI qui utilisent l’interface Microsoft Network Direct pour communiquer entre les instances. Pour l’instant, Network Direct est pris en charge seulement par MS-MPI (de Microsoft) pour Windows. Les instances A10 et A11 n’incluent pas l’accès au réseau RDMA.

>[AZURE.NOTE]Comme les autres instances Azure, les instances A8 et A9 peuvent exécuter des charges de travail autres que les applications MPI, en utilisant leurs cœurs de processeur, leur mémoire et leur espace disque disponibles. Dans ce cas cependant, les instances ne se connectent pas au réseau RDMA et elles sont fonctionnellement équivalentes aux instances A10 et A11.


Consultez le tableau suivant pour les configurations requises permettant aux applications MPI d’accéder au réseau RDMA dans les déploiements de machines virtuelles (IaaS) et de services cloud (PaaS) des instances A8 et A9. Pour les scénarios de déploiement classiques, consultez [Instances de calcul intensif A8 et A9 : démarrage rapide avec HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).


Configuration requise | Machines virtuelles (IaaS) | Services cloud (PaaS)
---------- | ------------ | -------------
Système d’exploitation | Machines virtuelles Windows Server 2012 R2 ou Windows Server 2012 | Famille de systèmes d’exploitation invités Windows Server 2012 R2 ou Windows Server 2008 R2
MPI | MS-MPI 2012 R2 ou ultérieur, autonome ou installé via HPC Pack 2012 R2 ou ultérieur | MS-MPI 2012 R2 ou ultérieur, installé via HPC Pack 2012 R2 ou ultérieur


>[AZURE.NOTE]Pour les scénarios IaaS, l’[extension HpcVmDrivers](https://msdn.microsoft.com/library/azure/dn690126.aspx) doit être ajouté aux machines virtuelles pour installer les pilotes Windows nécessaires à la connectivité RDMA.


## Autres informations à connaître

* Vous ne pouvez pas redimensionner une machine virtuelle Azure existante à la taille A8, A9, A10 ou A11.

* Les instances A8, A9, A10 et A11 ne peuvent pas actuellement être déployées en utilisant un service cloud qui fait partie d’un groupe d’affinités existant. De même, un groupe d’affinités avec un service cloud contenant des instances A8, A9, A10 et A11 ne peut pas être utilisé pour les déploiements d’autres tailles d’instance. Si vous tentez d’effectuer ces déploiements, vous verrez un message d’erreur similaire à `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`


## Étapes suivantes

* Pour plus d’informations sur la disponibilité et la tarification des instances A8, A9, A10 et A11, consultez [Tarification des machines virtuelles](http://azure.microsoft.com/pricing/details/virtual-machines/) et [Tarification des services cloud](http://azure.microsoft.com/pricing/details/cloud-services/).
* Pour une prise en main du déploiement et de l’utilisation d’instances A8 et A9 avec HPC Pack, consultez [Instances de calcul intensif A8 et A9 : démarrage rapide avec HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx) et [Exécuter des applications MPI sur des instances A8 et A9](https://msdn.microsoft.com/library/azure/dn592104.aspx).
 

<!---HONumber=62-->