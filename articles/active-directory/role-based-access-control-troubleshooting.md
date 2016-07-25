<properties
	pageTitle="Résolution des problèmes de contrôle d'accès en fonction du rôle | Microsoft Azure"
	description="Obtenez de l’aide en cas de problèmes ou de questions concernant les ressources de contrôle d’accès en fonction du rôle."
	services="azure-portal"
	documentationCenter="na"
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="kgremban"/>

# Résolution des problèmes de contrôle d’accès en fonction du rôle

## Introduction

Le [contrôle d’accès en fonction du rôle](role-based-access-control-configure.md) est une fonctionnalité puissante qui vous permet de déléguer l’accès affiné aux ressources dans Azure. Cela signifie que vous pouvez en toute sécurité accorder à la personne de votre choix le droit de faire ce qu’elle a besoin de faire, sans plus. Toutefois, le modèle de ressources pour les ressources Azure peut parfois être complexe, et il peut s'avérer difficile de comprendre avec précision pourquoi accorder certaines autorisations.

Ce document vous permet de savoir ce à quoi vous pouvez vous attendre quand vous utilisez les rôles sur le portail Azure. Ces trois rôles couvrent tous les types de ressources :

- Propriétaire
- Collaborateur
- Lecteur

Les propriétaires et collaborateurs disposent tous les deux d’un accès complet à toutes les opérations de gestion, mais un collaborateur ne peut pas accorder d’accès à d’autres utilisateurs ou groupes. La situation du rôle de lecteur est un peu plus intéressante, et nous allons nous y attarder. Consultez l’[article Prise en main du contrôle d’accès en fonction du rôle](role-based-access-control-configure.md) pour plus d’informations sur la façon d’octroyer l’accès.

## Charges de travail App Service

### Fonctionnalités d’accès en écriture

Si vous accordez un accès utilisateur en lecture seule à une seule application web, certaines fonctionnalités sont désactivées, ce que vous n’avez peut-être pas prévu. Les fonctionnalités de gestion suivantes exigent un accès en **écriture** à une application web (Collaborateur ou Propriétaire) et ne sont pas disponibles en lecture seule.

- Commandes (par ex., start, stop, etc.)
- Modification de paramètres tels que la configuration générale, les paramètres de mise à l’échelle, les paramètres de sauvegarde et les paramètres d’analyse
- Accès aux informations d’identification de publication et autres informations secrètes, telles que les paramètres d’application et les chaînes de connexion
- Diffusion de journaux
- Configuration des journaux de diagnostic
- Console (invite de commandes)
- Déploiements actifs et récents (pour le déploiement continu Git local)
- Estimation de dépense
- Tests web
- Réseau virtuel (accessible à un lecteur uniquement si un réseau virtuel a été précédemment configuré par un utilisateur avec accès en écriture).

Si vous n’accédez à aucune de ces vignettes, vous devez obtenir l’accès Collaborateur à l’application web auprès de votre administrateur.

### Gestion des ressources connexes

Les applications web sont compliqués par la présence de quelques ressources différentes qui interagissent. Voici un groupe de ressources classique avec deux sites web :

![Groupe de ressources d'application web](./media/role-based-access-control-troubleshooting/website-resource-model.png)

En conséquence, si vous accordez à un utilisateur le seul accès à l'application web, la plupart des fonctionnalités du volet Site web dans le portail Azure seront désactivées.

Les éléments suivants requièrent l'accès **en écriture** au **plan App Service** qui correspond à votre site web :

- Affichage du niveau de tarification de l'application web (Gratuit ou Standard)
- Configuration de mise à l'échelle (le nombre d'instances, la taille de la machine virtuelle, les paramètres de mise à l'échelle automatique)
- Quotas (stockage, bande passante, UC)

Les éléments suivants requièrent l'accès **en écriture** à l'ensemble du **Groupe de ressources** qui contient le site web :

- Certificats et liaisons SSL (car les certificats SSL peuvent être partagés entre différents sites dans le même groupe de ressources et emplacement)
- Règles d'alerte
- Paramètres de mise à l'échelle automatique
- Composants Application Insights
- Tests web

## Charges de travail des machines virtuelles

Comme pour les applications web, certaines fonctionnalités du volet de la machine virtuelle exigent un accès en écriture à la machine virtuelle ou à d'autres ressources du groupe de ressources.

Les machines virtuelles sont associées aux noms de domaine, réseaux virtuels, comptes de stockage et règles d'alerte.

Les éléments suivants requièrent l'accès **en écriture** à la **machine virtuelle** :

- Points de terminaison
- Adresses IP
- Disques
- Extensions

Les éléments suivants requièrent l’accès **en écriture** à la **machine virtuelle**, ainsi qu’au **Groupe de ressources** (avec le nom de domaine) auxquels ils appartiennent :

- Groupe à haute disponibilité
- Jeu d'équilibrage de la charge
- Règles d'alerte

Si vous n'accédez à aucune de ces vignettes, vous devez obtenir auprès de votre administrateur l'accès Collaborateur au groupe de ressources.

## En savoir plus
- [Contrôle d’accès en fonction du rôle Azure](role-based-access-control-configure.md) : découvrez le Contrôle d’accès en fonction du rôle Azure dans le portail Azure.
- [Rôles intégrés](role-based-access-built-in-roles.md) : obtenez des informations sur les rôles qui livrés en standard dans RBAC.
- [Rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure](role-based-access-control-custom-roles.md) : découvrez comment créer des rôles personnalisés selon vos besoins d'accès.
- [Créer un rapport d’historique des modifications d’accès](role-based-access-control-access-change-history-report.md) : effectuez le suivi des changements d'affection de rôle dans RBAC.

<!---HONumber=AcomDC_0713_2016-->