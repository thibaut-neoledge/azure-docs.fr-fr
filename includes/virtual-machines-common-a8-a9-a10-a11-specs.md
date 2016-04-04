

Cet article contient des informations et des considérations générales sur l’utilisation des instances Azure A8, A9, A10 et A11, également appelées instances de *calcul intensif*. Les fonctionnalités clés de ces instances sont les suivantes :

* **Matériel hautes performances** : le matériel du centre de données Azure exécutant ces instances a été conçu et optimisé pour les applications nécessitant des ressources réseau et de calcul importantes, notamment les applications en cluster pour du calcul hautes performances (HPC), la modélisation et les simulations.

* **Connexion réseau RDMA pour les applications MPI** : configurez les instances A8 et A9 pour communiquer avec d’autres instances A8 et A9 sur un réseau haut débit à faible latence dans Azure, basé sur la technologie d’accès direct à la mémoire à distance (RDMA). Cette fonctionnalité peut améliorer les performances de certaines applications MPI (Message Passing Interface) Linux et Windows.

* **Prise en charge des clusters HPC** : déployez un logiciel de gestion de cluster et de planification des travaux sur les instances A8, A9, A10 et A11 dans Azure pour créer un cluster HPC autonome ou pour ajouter de la capacité à un cluster local.

>[AZURE.NOTE]Les instances A10 et A11 ont les mêmes optimisations et spécifications en matière de performances que les instances A8 et A9. Cependant, elles n’incluent pas l’accès au réseau RDMA dans Azure. Ces instances sont conçues pour les applications de calcul hautes performances qui n’ont pas besoin d’une communication constante et à latence faible entre les nœuds. Ces applications sont également appelées applications paramétriques ou massivement parallèles.


## Spécifications

### Processeur et mémoire

Les instances de calcul intensif Azure A8, A9, A10 et A11 ont pour caractéristiques une vitesse élevée, des processeurs multicœurs et de grandes quantités de mémoire, comme le montre le tableau suivant.

Taille | UC | Mémoire
------------- | ----------- | ----------------
A8 et A10 | Intel Xeon E5-2670<br/>8 cœurs à 2,6 GHz | DDR3-1600 MHz<br/>56 Go
A9 et A11 | Intel Xeon E5-2670<br/>16 cœurs à 2,6 GHz | DDR3-1600 MHz<br/>112 Go


>[AZURE.NOTE]Vous pouvez trouver des informations plus détaillées, y compris les extensions du jeu d’instructions pris en charge, sur le site Web Intel.com. Pour plus d’informations sur les capacités de stockage des machines virtuelles et sur les disques, consultez [Tailles des machines virtuelles](virtual-machines-linux-sizes.md).

### Adaptateurs réseau

Les instances A8 et A9 ont deux cartes réseau, qui se connectent aux réseaux Azure principaux suivants.


Réseau | Description
-------- | -----------
Ethernet 10 Gbits/s | Se connecte aux services Azure (comme le stockage Azure et Azure Virtual Network) et à Internet
Principal 32 Gbits/s, compatible RDMA | Permet une communication des applications haut débit à latence faible entre les instances au sein d’un même service cloud ou d’un même groupe à haute disponibilité. Réservé uniquement au trafic MPI.


>[AZURE.IMPORTANT]Consultez la section [Accès au réseau RDMA](#access-the-rdma-network) de cet article afin de connaître les exigences supplémentaires des applications MPI pour accéder au réseau RDMA.

Les instances A10 et A11 ont une seule carte réseau Ethernet 10 Gbits/s qui se connecte aux services Azure et à Internet.

## Points à prendre en considération pour le déploiement

* **Compte Azure** : si vous voulez déployer davantage qu’un petit nombre d’instances de calcul intensif, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Vous pouvez également utiliser votre abonnement MSDN. Consultez [Avantages d’Azure pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/pricing/free-trial/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

* **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs de votre abonnement Azure, qui est, par défaut, de 20 cœurs par abonnement (si vous utilisez le modèle de déploiement classique) ou de 20 cœurs par région (si vous utilisez le modèle de déploiement Azure Resource Manager). Pour obtenir une augmentation du quota, ouvrez un ticket de support gratuit comme indiqué dans [Présentation des limites et des augmentations Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

    >[AZURE.NOTE]Les quotas d’Azure sont des limites de crédit et non des garanties de capacité. Vous êtes facturé seulement pour les cœurs que vous utilisez.

* **Réseau virtuel** : un [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) Azure n’est pas requis pour utiliser les instances qui nécessitent beaucoup de ressources système. Cependant, vous pouvez avoir besoin d’au moins un réseau virtuel Azure dans le cloud pour nombre de scénarios IaaS, ou d’une connexion de site à site si vous devez accéder à des ressources locales, par exemple à un serveur de licences d’application. Vous devrez créer un réseau virtuel avant de déployer les instances. L’ajout d’une machine virtuelle A8, A9, A10 ou A11 à un réseau virtuel dans un groupe d’affinités n’est pas pris en charge.

* **Service cloud ou groupe à haute disponibilité** : pour vous connecter via le réseau RDMA, les instances A8 et A9 doivent être déployées dans le même service cloud (si vous utilisez le modèle de déploiement classique) ou le même groupe à haute disponibilité (si vous utilisez le modèle de déploiement Azure Resource Manager).

* **Tarification** : les tailles de machine virtuelle A8 à A11 sont uniquement disponibles dans le niveau tarifaire Standard.

* **Redimensionnement** : vous ne pouvez pas redimensionner une instance d'une taille différente de A8 à A11 pour l'une des tailles d'instance de calcul intensif (A8 à 11) et vous ne pouvez pas redimensionner une instance de calcul intensif sur une taille de calcul non intensif. Cela est dû au matériel spécialisé et aux optimisations des performances qui sont spécifiques aux instances de calcul intensif.

* **Espace d'adressage réseau RDMA** : le réseau RDMA dans Azure réserve l'espace d’adressage 172.16.0.0/12. Si vous envisagez d'exécuter des applications MPI sur des instances A8 et A9 dans un réseau virtuel Azure, assurez-vous que l'espace d'adressage du réseau virtuel ne chevauche pas le réseau RDMA.

<!---HONumber=AcomDC_0323_2016-->