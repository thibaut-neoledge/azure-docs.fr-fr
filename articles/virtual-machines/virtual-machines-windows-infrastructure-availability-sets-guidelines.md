<properties
	pageTitle="Instructions pour les groupes à haute disponibilité | Microsoft Azure"
	description="Découvrez-en plus sur les principales instructions de conception et d’implémentation pour le déploiement de groupes à haute disponibilité dans des services d’infrastructure Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="iainfou"/>

# Instructions pour les groupes à haute disponibilité

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Cet article se concentre sur la compréhension des étapes de planification requises pour les groupes à haute disponibilité afin d’assurer que vos applications restent accessibles aux cours des événements planifiés et non planifiés.

## Instructions d’implémentation pour groupes à haute disponibilité

Décisions :

- De combien de groupes à haute disponibilité avez-vous besoin pour les différents rôles et niveaux de votre infrastructure d’application ?

Tâches :

- Définissez le nombre de machines virtuelles requises pour chaque niveau d’application.
- Déterminez si vous devez ajuster le nombre de domaines d’erreur ou de mise à jour à utiliser pour votre application.
- Définissez les groupes à haute disponibilité requis à l’aide de votre convention de dénomination et des machines virtuelles qui s’y trouveront. Une machine virtuelle ne peut résider que dans un seul groupe à haute disponibilité.

## Groupes à haute disponibilité

Dans Azure, les machines virtuelles peuvent être placées dans un groupement logique appelé groupe à haute disponibilité. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure répartit le placement de ces machines virtuelles sur l’infrastructure sous-jacente. En cas d’événement de maintenance planifiée pour la plateforme Azure ou de panne de l’infrastructure ou du matériel sous-jacent, l’utilisation des groupes à haute disponibilité assure qu’au moins une machine virtuelle est en cours d’exécution.

En tant que meilleure pratique, les applications ne doivent pas résider sur une seule machine virtuelle. Un groupe à haute disponibilité qui contient une seule machine virtuelle ne gagne aucune protection contre les événements planifiés ou non planifiés dans la plateforme Azure. Le [Contrat de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) nécessite deux machines virtuelles ou plus au sein d’un groupe à haute disponibilité défini afin de permettre la distribution des machines virtuelles sur l’infrastructure sous-jacente.

L’infrastructure sous-jacente dans Azure est divisée en domaines de mise à jour et domaines d’erreur. Ces domaines sont définis par les hôtes qui partageront un cycle de mise à jour commun, ou une infrastructure physique similaire, comme l’alimentation ou la mise en réseau. Azure distribue automatiquement vos machines virtuelles au sein d’un groupe à haute disponibilité sur plusieurs domaines pour assurer la disponibilité et la tolérance aux pannes. Selon la taille de votre application et le nombre de machines virtuelles au sein d’un groupe à haute disponibilité, vous pouvez régler le nombre de domaines que vous souhaitez utiliser. Vous pouvez en savoir plus sur [la gestion de la disponibilité et l’utilisation des domaines d’erreur et de mise à jour](virtual-machines-windows-manage-availability.md).

Lorsque vous concevez votre infrastructure d’application, vous devez également planifier les couches d’application que vous allez utiliser. Groupez les machines virtuelles qui ont la même fonction dans des groupes à haute disponibilité définis, comme un groupe à haute disponibilité pour vos machines virtuelles frontales exécutant IIS. Créez un groupe à haute disponibilité distinct pour les machines virtuelles principales exécutant SQL Server. L’objectif est de vous assurer que chaque composant de votre application est protégé par un groupe à haute disponibilité et qu’au moins une instance soit toujours exécutée.

Les équilibreurs de charge peuvent être utilisés devant chaque couche d’application pour fonctionner avec un groupe à haute disponibilité et assurer que le trafic peut être acheminé vers une instance en cours d’exécution. Sans équilibreur de charge, vos machines virtuelles peuvent continuer à s’exécuter dans l’ensemble des événements de maintenance planifiée et non planifiée, mais vos utilisateurs finaux pourraient ne pas être en mesure de les résoudre si la machine virtuelle principale n’est pas disponible à ce moment.


## Étapes suivantes
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0706_2016-->