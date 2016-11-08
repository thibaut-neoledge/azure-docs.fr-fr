---
title: Concepts de DevTest Labs | Microsoft Docs
description: Découvrez les concepts de base de DevTest Labs et comment il peut faciliter la création, la gestion et la surveillance des machines virtuelles Azure
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: tarcher

---
# Concepts de DevTest Labs
> [!NOTE]
> Cet article est la 3e partie d’une série de 3 articles :
> 
> 1. [Qu’est-ce que DevTest Labs ?](devtest-lab-overview.md)
> 2. [Pourquoi utiliser DevTest Labs ?](devtest-lab-why.md)
> 3. **[Concepts de DevTest Labs](devtest-lab-concepts.md)**
> 
> 

## Vue d'ensemble
La liste suivante présente les définitions et concepts principaux de DevTest Labs :

## Artefacts
Les artefacts sont utilisés pour déployer et configurer votre application après l’approvisionnement d’une machine virtuelle. Les artefacts peuvent être :

* des outils que vous voulez installer sur la machine virtuelle, tels que des agents, Fiddler, Visual Studio ;
* des actions que vous souhaitez exécuter sur la machine virtuelle, telles que le clonage d’un dépôt ;
* des applications que vous voulez tester.

Les artefacts sont des fichiers JSON [Azure Resource Manager](../resource-group-overview.md) qui contiennent des instructions pour effectuer le déploiement et appliquer la configuration.

## Référentiels d’artefact
Les référentiels d’artefact sont des dépôts Git dans lesquels les artefacts sont archivés. Vous pouvez ajouter un même dépôt d’artefact à plusieurs laboratoires de votre organisation pour les réutiliser et les partager.

## Images de base
Les images de base sont des images de machine virtuelle dont tous les outils et paramètres sont préinstallés et configurés pour créer rapidement une machine virtuelle. Vous pouvez approvisionner une machine virtuelle en sélectionnant une base existante, puis en ajoutant un artefact pour installer l’agent de test. Vous pouvez ensuite enregistrer la machine virtuelle approvisionnée comme base. Il est donc possible d’utiliser la base sans avoir à réinstaller l’agent de test pour chaque approvisionnement de votre machine virtuelle.

## Formules
Par rapport aux images de base, les formules offrent en plus un mécanisme pour l’approvisionnement rapide de machines virtuelles. Dans DevTest Labs, une formule est une liste de valeurs de propriétés par défaut utilisée pour créer une machine virtuelle de laboratoire. Avec les formules, les machines virtuelles ayant le même ensemble de propriétés, comme l’image de base, la taille de machine virtuelle, le réseau virtuel et les artefacts, peuvent être créées sans avoir à spécifier ces propriétés à chaque fois. Quand vous créez une machine virtuelle à partir d’une formule, les valeurs par défaut peuvent être utilisées telles quelles ou modifiées.

## Limites
Les plafonds constituent un mécanisme permettant de réduire le gaspillage dans votre laboratoire. Par exemple, vous pouvez définir un plafond pour limiter le nombre de machines virtuelles qui peuvent être créées par un utilisateur ou dans un laboratoire.

## Stratégies
Les stratégies vous aident à contrôler les coûts dans votre laboratoire. Par exemple, vous pouvez créer une stratégie pour arrêter automatiquement les machines virtuelles selon une planification définie.

## Niveaux de sécurité
L’accès à la sécurité est déterminé par le contrôle d’accès en fonction du rôle (RBAC) d’Azure. Pour comprendre comment l’accès est déterminé, vous devez saisir les différences entre une autorisation, un rôle et une étendue, comme défini par RBAC.

* Autorisation - Une autorisation est un accès défini pour une action spécifique (p. ex. accès en lecture à toutes les machines virtuelles).
* Rôle - un rôle est un jeu d’autorisations qui peuvent être regroupées et attribuées à un utilisateur. Par exemple, le rôle *propriétaire de l’abonnement* a accès à toutes les ressources au sein d’un abonnement.
* Étendue - Une étendue est un niveau dans la hiérarchie des ressources Azure (comme un groupe de ressources ou un simple laboratoire ou l’ensemble de l’abonnement).

Dans l’étendue de DevTest Labs, il existe deux types de rôles pour définir des autorisations utilisateur : propriétaire de laboratoire et utilisateur de laboratoire.

* Propriétaire de laboratoire - un propriétaire de laboratoire a accès à toutes les ressources du laboratoire. Ainsi, le propriétaire d’un laboratoire peut modifier les stratégies, lire et écrire sur toutes les machines virtuelles, modifier le réseau virtuel et ainsi de suite.
* Utilisateur de laboratoire : un utilisateur de laboratoire peut afficher toutes les ressources de laboratoire, telles que les machines virtuelles, les stratégies et les réseaux virtuels, mais il ne peut pas modifier les stratégies ou les machines virtuelles créées par d’autres utilisateurs.

Pour découvrir comment créer des rôles personnalisés dans DevTest Labs, consultez l’article [Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Étant donné que les étendues sont hiérarchiques, lorsqu’un utilisateur dispose d’autorisations pour une certaine étendue, il reçoit automatiquement ces autorisations pour chaque niveau d’étendue inférieur englobé. Par exemple, si un utilisateur est affecté au rôle de propriétaire d’abonnement, il a accès à toutes les ressources dans un abonnement, notamment à toutes les machines virtuelles, tous les réseaux virtuels et tous les laboratoires. Ainsi, un propriétaire d’abonnement hérite automatiquement du rôle de propriétaire de laboratoire. Toutefois, l’inverse n’est pas vrai. Un propriétaire de laboratoire a accès à un laboratoire, qui est une étendue inférieure au niveau d’abonnement. Par conséquent, un propriétaire de laboratoire ne sera pas en mesure de voir les machines virtuelles ou réseaux virtuels ou toutes les ressources qui sont en dehors du laboratoire.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Étapes suivantes
[Créer un laboratoire dans DevTest Labs](devtest-lab-create-lab.md)

<!---HONumber=AcomDC_0831_2016-->