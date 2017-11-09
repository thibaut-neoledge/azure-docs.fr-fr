---
title: "Présentation d’Azure Container Service pour Kubernetes | Microsoft Docs"
description: "Azure Container Service pour Kubernetes simplifie le déploiement et la gestion des applications en conteneur dans Azure."
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Kubernetes, docker, conteneurs, microservices, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: fa46d16e2105c0354cc533c58fb9e0093c129ced
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Présentation d’Azure Container Service (AKS)

Azure Container Service (AKS) simplifie la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications en conteneur. Il vous permet d’exploiter vos compétences existantes ou de faire appel à une large communauté d’experts toujours plus nombreux pour déployer et gérer des applications en conteneur sur Microsoft Azure.

En utilisant AKS, vous pouvez tirer parti des fonctionnalités d’entreprise d’Azure tout en conservant la portabilité des applications par le biais de Kubernetes et du format d’image Docker.

## <a name="using-azure-container-service-aks"></a>Utilisation d’Azure Container Service (AKS)
L’objectif d’AKS est de proposer un environnement d’hébergement de conteneurs basé sur des outils et des technologies open source déjà bien connus de nos clients. Dans cette optique, nous présentons les points de terminaison standards de l’API Kubernetes. En utilisant ces points de terminaison standards, vous pouvez exploiter n’importe quel logiciel capable de communiquer avec un cluster Kubernetes. Par exemple, vous pourriez choisir [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/), ou [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Création d’un cluster Kubernetes à l’aide d’Azure Container Service (AKS)
Pour commencer à utiliser ACS, déploiement d’un cluster ACS avec la [CLI d’Azure](./kubernetes-walkthrough.md) ou via le portail (Rechercher sur le Marketplace pour **Service de conteneur Azure**). Si vous êtes un utilisateur expérimenté ayant désireux d’avoir davantage de contrôle sur les modèles Azure Resource Manager, vous pouvez utiliser le projet open source [acs-engine](https://github.com/Azure/acs-engine) pour générer votre propre cluster Kubernetes personnalisé et le déployer via `az` CLI.

### <a name="using-kubernetes"></a>Utilisation de Kubernetes
Kubernetes automatise le déploiement, la mise à l’échelle et la gestion des applications en conteneur. Il possède un jeu complet de fonctionnalités, notamment :
* Binpacking automatique
* Réparation spontanée
* Mise à l’échelle horizontale
* Détection de service et équilibrage de charge
* Déploiements et restaurations automatisés
* Secret et gestion de la configuration
* Orchestration de stockage
* Exécution Batch

## <a name="videos"></a>Vidéos

Prise en charge de Kubernetes dans Azure Container Service (Azure Friday janvier 2017) :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Outils dédiés au développement et au déploiement d’applications sur Kubernetes (Azure OpenDev juin 2017) :

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

En savoir plus sur le déploiement et la gestion des ACS avec ACS démarrage rapide.

> [!div class="nextstepaction"]
> [Didacticiel ACS](./kubernetes-walkthrough.md)