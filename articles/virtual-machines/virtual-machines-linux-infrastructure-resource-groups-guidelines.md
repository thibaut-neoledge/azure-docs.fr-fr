<properties
	pageTitle="Instructions pour les groupes de ressources | Microsoft Azure"
	description="Découvrez-en plus sur les principales instructions de conception et d’implémentation pour le déploiement de groupes de ressources dans des services d’infrastructure Azure."
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

# Instructions pour les groupes de ressources Azure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Cet article se concentre sur la compréhension de la création logique de votre environnement et du regroupement de tous les composants dans des groupes de ressources.


## Instructions d’implémentation pour les groupes de ressources

Décisions :

- Allez-vous créer des groupes de ressources avec les composants de l’infrastructure principale, ou avec un déploiement de l’application complète ?
- Devez-vous restreindre l’accès aux groupes de ressources à l’aide de contrôles d’accès en fonction du rôle ?

Tâches :

- Définissez les composants de l’infrastructure principale dont vous aurez besoin et si vous souhaitez utiliser des groupes de ressources dédiés.
- Déterminez comment implémenter les modèles Resource Manager pour des déploiements cohérents, reproductibles.
- Définissez les rôles d’accès utilisateur dont vous aurez besoin pour accéder aux groupes de ressources.
- Créer l’ensemble de groupes de ressources à l’aide de votre convention de dénomination. Vous pouvez utiliser le portail ou l’interface de ligne de commande Azure.


## Groupes de ressources

Dans Azure, vous pouvez regrouper logiquement des ressources connexes comme les comptes de stockage, les réseaux virtuels et les machines virtuelles afin de les déployer, gérer et préserver comme une seule entité. Cela simplifie le déploiement d’applications tout en gardant toutes les ressources liées ensemble d’un point de vue gestion, ou pour accorder aux autres l’accès à ce groupe de ressources. Pour en savoir plus sur les groupes de ressources, vous pouvez lire la [Présentation d’Azure Resource Manager](../resource-group-overview.md).

Une fonctionnalité clé des groupes de ressources est la possibilité de créer votre environnement à l’aide d’un fichier JSON qui déclare le stockage, réseau et les ressources de calcul, ainsi que des scripts personnalisés connexes ou des configurations à appliquer. Avec ces modèles JSON, vous pouvez créer des déploiements cohérents, reproductibles pour vos applications. Cela simplifie la création d’un environnement de développement, puis l’utilisation de ce même modèle pour créer un déploiement de production, ou vice versa. Pour mieux comprendre l’utilisation des modèles, vous pouvez lire [la procédure pas à pas du modèle](../resource-manager-template-walkthrough.md), qui vous guidera à chaque étape de la création d’un modèle JSON.

Il existe deux approches différentes, que vous pouvez suivre lors de la conception de votre environnement avec des groupes de ressources :

- Des groupes de ressources pour chaque déploiement d’application qui combine les comptes de stockage, les réseaux et les sous-réseaux virtuels, les machines virtuelles, les équilibrages de charge, etc..
- Des groupes de ressources centralisés contenant votre réseau et vos sous-réseaux virtuels ou comptes de stockage de base, avec vos applications placées dans leurs propres groupes de ressources ces derniers comptant uniquement des machines virtuelles, des équilibrages de charge, des interfaces réseau, etc.

Lorsque vous augmentez la taille des instances, la création de groupes de ressources centralisés simplifie la création de connexions réseau locales pour des options de connectivité hybride, plutôt que d’avoir chaque application disposant de son propre réseau virtuel, ce qui nécessite une configuration et une maintenance à chaque fois. Les [Contrôles d’accès en fonction du rôle](../active-directory/role-based-access-control-what-is.md) offrent une façon granulaire d’accéder aux groupes de ressources. Pour les applications de production, vous pouvez contrôler les utilisateurs qui peuvent accéder à ces ressources, ou pour les ressources d’infrastructure principale, vous pouvez restreindre l’accès aux ingénieurs d’infrastructure pour travailler avec eux. Les propriétaires des applications auront uniquement accès aux composants au sein de leur groupe de ressources et non au coeur de l’infrastructure Azure de votre environnement. Lorsque vous concevez votre environnement, garde à l’esprit les utilisateurs qui nécessitent un accès aux ressources et concevez vos groupes de ressources en conséquence.


## Étapes suivantes

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0706_2016-->