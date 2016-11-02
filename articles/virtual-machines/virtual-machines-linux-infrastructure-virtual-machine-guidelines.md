<properties
	pageTitle="Instructions pour les machines virtuelles Linux | Microsoft Azure"
	description="Découvrez-en plus sur les principales instructions de conception et d’implémentation pour le déploiement de machines virtuelles Linux dans Azure"
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
	ms.date="09/08/2016"
	ms.author="iainfou"/>

# Instructions pour les machines virtuelles

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Cet article se concentre sur la compréhension des étapes de planification nécessaires pour créer et gérer des machines virtuelles dans votre environnement Azure.

## Instructions d’implémentation pour les machines virtuelles
Décisions :

- De combien de machines virtuelles avez-vous besoin pour les diverses couches d’application et les composants de votre infrastructure ?
- De quelles ressources processeur et mémoire les machines virtuelles ont-elles besoin, et quelles sont les exigences de stockage ?

Tâches :

- Définissez les charges de travail pour votre application et les ressources nécessaires pour les machines virtuelles.
- Alignez les demandes de ressources pour chaque ordinateur virtuel avec le bon type de stockage et la bonne taille de machine virtuelle.
- Définissez vos groupes de ressources pour les couches et composants de votre infrastructure.
- Définissez votre convention de dénomination de machines virtuelles.
- Créez vos machines virtuelles à l’aide de l’interface de ligne de commande Azure, le portail web ou des modèles Resource Manager.

## Machines virtuelles

Les machines virtuelles sont probablement un des principaux composants au sein de votre environnement Azure. C’est ici que vous exécutez vos applications, bases de données, services d’authentification, etc.

Il est important de comprendre les [différentes tailles de machines virtuelles](virtual-machines-linux-sizes.md) pour dimensionner correctement votre environnement du point de vue des performances et du coût. Si vos machines virtuelles n’ont pas assez de cœurs de processeur ou de mémoire, les performances de votre application en sont affectées, quelle que soit la façon dont elle est conçue et développée. Vérifiez les charges de travail suggérées pour chaque série de machines virtuelles comme point de départ lorsque vous décidez de la taille de machine virtuelle à utiliser pour chaque composant de votre infrastructure. Vous pouvez [modifier la taille d’une machine virtuelle](virtual-machines-linux-change-vm-size.md) après le déploiement.

Le stockage joue un rôle clé dans les performances des machines virtuelles. Vous pouvez utiliser le stockage Standard, qui emploie des disques rotatifs ordinaires, ou le stockage Premium pour les charges de travail gourmandes en E/S et des performances de pointe, qui utilise des disques SSD. Comme avec la taille de la machine virtuelle, vous devez envisager les coûts lorsque vous sélectionnez le support de stockage. Vous pouvez lire [l’article d’instructions pour les infrastructures de stockage](virtual-machines-linux-infrastructure-storage-solutions-guidelines.md) pour comprendre comment concevoir le stockage approprié pour optimiser les performances de vos machines virtuelles.


## Groupes de ressources
Des composants comme les machines virtuelles sont regroupés logiquement pour faciliter la gestion et la maintenance avec les [groupes de ressources Azure](../resource-group-overview.md). En utilisant les groupes de ressources, vous pouvez créer, gérer et surveiller des ressources qui composent une application donnée. Vous pouvez également implémenter les [contrôles d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md) pour accorder l’accès à d’autres personnes au sein de votre équipe aux seules ressources dont ils ont besoin. Prenez le temps de planifier vos groupes de ressources et les affectations de rôles. Il existe différentes approches pour réellement concevoir et implémenter des groupes de ressources, veillez à lire [l’article d’instructions pour les groupes de ressources](virtual-machines-linux-infrastructure-resource-groups-guidelines.md) pour comprendre la meilleure approche pour créer vos machines virtuelles.


## Modèles 
Vous pouvez créer des modèles, définis par des fichiers JSON déclaratifs pour créer vos machines virtuelles. Généralement, les modèles créent aussi le stockage, le réseau, les interfaces réseau, les adresses IP, etc. nécessaires en plus des machines virtuelles elles-mêmes. Vous pouvez utiliser des modèles pour créer des environnements cohérents et reproductibles à des fins de développement et de test afin de répliquer facilement les environnements de production et inversement. Vous pouvez en savoir plus sur [la création et l’utilisation de modèles](../resource-group-overview.md#template-deployment) pour comprendre comment les utiliser pour créer et déployer vos machines virtuelles.


## Étapes suivantes
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->