---
title: Groupes de ressources pour les machines virtuelles Linux dans Azure | Microsoft Docs
description: "Découvrez-en plus sur les principales instructions de conception et d’implémentation pour le déploiement de groupes de ressources dans des services d’infrastructure Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 93ab9d93-965a-46b9-9c87-a10d652a6422
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: bbf0f4868a95d21c3aaa31109c4c5a8083efc6db
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# Instructions pour les groupes de ressources Azure pour les machines virtuelles Linux
<a id="azure-resource-group-guidelines-for-linux-vms" class="xliff"></a> 

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Cet article se concentre sur la compréhension de la création logique de votre environnement et du regroupement de tous les composants dans des groupes de ressources.

## Instructions d’implémentation pour les groupes de ressources
<a id="implementation-guidelines-for-resource-groups" class="xliff"></a>
Décisions :

* Allez-vous créer des groupes de ressources avec les composants de l’infrastructure principale, ou avec un déploiement de l’application complète ?
* Devez-vous restreindre l’accès aux groupes de ressources à l’aide de contrôles d’accès en fonction du rôle ?

Tâches :

* Définissez les composants de l’infrastructure principale et les groupes de ressources dédiés dont vous avez besoin.
* Déterminez comment implémenter les modèles Resource Manager pour des déploiements cohérents, reproductibles.
* Définissez les rôles d’accès utilisateur dont vous avez besoin pour accéder aux groupes de ressources.
* Créer l’ensemble de groupes de ressources à l’aide de votre convention de dénomination. Vous pouvez utiliser le portail ou l’interface de ligne de commande Azure.

## Groupes de ressources
<a id="resource-groups" class="xliff"></a>
Dans Azure, vous regroupez logiquement des ressources connexes comme les comptes de stockage, les réseaux virtuels et les machines virtuelles afin de les déployer, de les gérer et de les préserver comme une seule entité. Cette approche simplifie le déploiement d’applications tout en gardant toutes les ressources liées ensemble du point de vue de la gestion, ou pour accorder aux autres l’accès à ce groupe de ressources. Les noms des groupes de ressources peuvent avoir une longueur maximale de 90 caractères. Pour en savoir plus sur les groupes de ressources, vous pouvez lire la [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Une fonctionnalité clé des groupes de ressources est la possibilité de créer votre environnement à l’aide d’un fichier JSON qui déclare les ressources de stockage, de réseau et de calcul. Vous pouvez également définir des scripts ou des configurations personnalisés connexes à appliquer. Avec ces modèles JSON, vous créez des déploiements cohérents, reproductibles pour vos applications. Cette approche vous permet de créer un environnement de développement, puis d’utiliser de ce même modèle pour créer un déploiement de production, ou vice versa. Pour mieux comprendre l’utilisation des modèles, lisez [la procédure pas à pas du modèle](../../azure-resource-manager/resource-manager-template-walkthrough.md) , qui vous guide à chaque étape de la création d’un modèle JSON.

Il existe deux approches différentes, que vous pouvez suivre lors de la conception de votre environnement avec des groupes de ressources :

* Des groupes de ressources pour chaque déploiement d’application qui combine les comptes de stockage, les réseaux et les sous-réseaux virtuels, les machines virtuelles, les équilibrages de charge, etc.
* Des groupes de ressources centralisés qui contiennent votre réseau et vos sous-réseaux virtuels principaux ou vos comptes de stockage. Vos applications se trouvent alors dans leur propre groupe de ressources, qui contient uniquement les machines virtuelles, les équilibreurs de charge, les interfaces réseau, etc.

Lorsque vous augmentez la taille des instances, la création de groupes de ressources centralisés simplifie la création de connexions réseau locales pour des options de connectivité hybride. L’autre approche consiste en ce que chaque application dispose de son propre réseau virtuel, ce qui nécessite une configuration et une maintenance. [Contrôles d’accès en fonction du rôle](../../active-directory/role-based-access-control-what-is.md) offrent une façon granulaire d’accéder aux groupes de ressources. Pour les applications de production, vous pouvez contrôler les utilisateurs qui peuvent accéder à ces ressources, ou pour les ressources d’infrastructure principale, vous pouvez restreindre l’accès aux ingénieurs d’infrastructure pour travailler avec eux. Les propriétaires des applications ont uniquement accès aux composants au sein de leur groupe de ressources et non au cœur de l’infrastructure Azure de votre environnement. Lorsque vous concevez votre environnement, gardez à l’esprit les utilisateurs qui ont besoin d’un accès aux ressources et concevez vos groupes de ressources en conséquence. 

## Étapes suivantes
<a id="next-steps" class="xliff"></a>
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


