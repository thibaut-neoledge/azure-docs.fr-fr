<properties
	pageTitle="Instructions pour solutions de stockage | Microsoft Azure"
	description="Découvrez-en plus sur les principales instructions de conception et d’implémentation pour le déploiement de solutions de stockage dans des services d’infrastructure Azure."
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="iainfou"/>

# Instructions pour les infrastructures de stockage

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Cet article se concentre sur la compréhension des besoins de stockage et les considérations de conception pour obtenir des performances de machine virtuelle optimales.


## Instructions d’implémentation pour le stockage

Décisions :

- Devez-vous utiliser le stockage Standard ou Premium pour votre charge de travail ?
- Avez-vous besoin d’un entrelacement pour créer des disques d’une taille supérieure à 1 023 Go ?
- Avez-vous besoin d’un entrelacement pour optimiser les performances d’E/S de votre charge de travail ?
- Quel est l’ensemble de comptes de stockage dont vous avez besoin pour héberger votre charge de travail ou votre infrastructure informatique ?

Tâches :

- Passez en revue les demandes d’E/S des applications que vous allez déployer et planifiez le numéro et le type de compte de stockage.
- Créer l’ensemble de comptes de stockage à l’aide de votre convention d’affectation de noms. Vous pouvez utiliser le portail ou l’interface de ligne de commande Azure.


## Storage

Azure Storage est un élément essentiel du déploiement et de la gestion des applications et des machines virtuelles. Azure Storage fournit des services pour le stockage des données de fichiers, les données non structurées et les messages. Il fait également partie de l’infrastructure de prise en charge des machines virtuelles.

Il existe deux types de comptes de stockage disponibles dans pour prendre en charge les machines virtuelles :

- Un compte de stockage standard vous donne accès au stockage d’objets blob (utilisé pour le stockage de disques de machines virtuelles Azure), de tables, de files d’attente et de fichiers de stockage
- [Le stockage Premium](../storage/storage-premium-storage.md) est offre une prise en charge de disques hautes performances à faible latence pour les charges de travail gourmandes en E/S, telles que les serveurs SQL dans un cluster AlwaysOn, et prend actuellement en charge uniquement les disques de machine virtuelle Azure.

Azure crée des machines virtuelles avec un disque de système d’exploitation, et éventuellement plusieurs disques de données facultatifs. Le disque de système d’exploitation et les disques de données sont des objets blob de pages Azure, tandis que le disque temporaire est stocké localement sur le nœud comprenant l’emplacement de la machine. Lors de la conception d’applications, veillez à utiliser uniquement ce disque temporaire pour des données non persistantes, car la machine virtuelle peut être migrée entre ordinateurs hôtes pendant un événement de maintenance. Toutes les données stockées sur le disque temporaire seraient alors perdues.

La durabilité et la haute disponibilité sont fournies par l’environnement Azure Storage sous-jacent afin de garantir que vos données restent protégées contre les défaillances matérielles et les maintenances non planifiées. Lorsque vous concevez votre environnement Azure Storage, vous pouvez choisir de répliquer de stockage de machine virtuelle localement dans un centre de données Azure donné, entre des centres de données Azure dans une région donnée, ou même entre des centres de données Azure dans des régions différentes. Vous pouvez lire [plus d’informations sur les options de réplication pour la haute disponibilité](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Les disques de système d’exploitation et les disques de données ont une taille maximale de 1 023 Go, étant donné que la taille maximale d’un objet blob est de 1 024 Go et qu’il doit contenir les métadonnées (pied de page) du fichier VHD (un Go compte 1 024<sup>3</sup> octets). Vous pouvez utiliser le Gestionnaire de volumes logiques (LVM) pour dépasser cette limite en regroupant des disques de données pour présenter des volumes logiques plus de 1 023 Go à votre machine virtuelle.

Il existe certaines limites d’évolutivité lors de la conception de vos déploiements Azure Storage : voir [Abonnement Microsoft Azure et limites, quotas et contraintes du service](azure-subscription-service-limits.md#storage-limits) pour plus de détails. Voir également [Objectifs de performance et d’extensibilité d’Azure Storage](../storage/storage-scalability-targets.md).

En ce qui concerne le stockage d’applications, vous pouvez stocker les données d’objets non structurées comme des documents, des images, des sauvegardes, des données de configuration, des journaux, etc. à l’aide du stockage d’objets blob. Plutôt que d’écrire sur un disque virtuel connecté à la machine virtuelle, l’application peut écrire directement sur le stockage d’objets blob. Le stockage d’objets blob offre également la possibilité de choisir entre [des niveaux de stockage à chaud et à froid](../storage/storage-blob-storage-tiers.md) selon vos besoins de disponibilité et les contraintes de coût.


## Disques agrégés par bandes
Outre la possibilité de créer des disques d’une taille supérieure à 1 023 Go dans plusieurs instances, l’entrelacement de disques améliore les performances en permettant à plusieurs objets blob de sauvegarder le stockage d’un seul volume. Avec l’agrégation par bandes, l’E/S requise pour écrire et lire des données à partir d’un seul disque logique est exécutée en parallèle.

Azure impose des limites quant à la quantité de disques de données et de bande passante disponible, selon la taille de la machine virtuelle. Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).

Si vous utilisez l’entrelacement pour les disques de données Azure, respectez les consignes suivantes :

- Les disques de données doivent toujours avoir la taille maximale (1 023 Go)
- Attachez le nombre maximum autorisé de disques de données pour la taille de machine virtuelle
- Utilisation de LVM
- Évitez d’utiliser des options de mise en cache des disques de données Azure (Stratégie de mise en cache = Aucune)

Pour plus d’informations, voir la page [Espaces de stockage : une conception pour la performance](http://social.technet.microsoft.com/wiki/contents/articles/15200.storage-spaces-designing-for-performance.aspx).


## Comptes de stockage multiples

Lorsque vous concevez votre environnement Azure Storage, vous pouvez utiliser plusieurs comptes de stockage lorsque le nombre de machines virtuelles que vous déployez augmente. Cela permet de répartir les E/S sur l’infrastructure Azure Storage sous-jacente afin de maintenir des performances optimales pour vos machines virtuelles et vos applications. Lorsque vous concevez des applications qui seront déployées, prenez en compte les exigences d’E/S de chaque machine virtuelle et équilibrez ces machines virtuelles à travers les comptes Azure Storage. Essayez d’éviter de grouper des VM gourmandes en E/S sur un ou deux comptes seulement.

Pour plus d’informations sur les fonctionnalités d’E/S des différentes options d’Azure Storage et des valeurs maximales recommandées, consultez les [Objectifs de performance et d’évolutivité d’Azure Storage](../storage/storage-scalability-targets.md).


## Étapes suivantes

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0629_2016-->