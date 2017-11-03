---
title: "Présentation d’OpenShift sur Azure | Microsoft Docs"
description: "Présentation d’OpenShift sur Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>Présentation d’OpenShift

OpenShift est une plateforme d’applications conteneur ouverte et extensible qui rend Docker et Kubernetes accessibles aux entreprises.  

OpenShift inclut Kubernetes pour la gestion et l’orchestration du conteneur. Il offre également des outils de développement centrés sur les opérations qui permettent :

- Un développement d’applications rapide
- Un déploiement et une mise à l’échelle plus simples
- La maintenance du cycle de vie à long terme des applications et des équipes

Il existe différentes versions d’OpenShift dont deux sont compatibles avec Azure.

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

Parmi ces quatre versions, deux sont compatibles avec un déploiement dans Azure par les clients : OpenShift Origin et OpenShift Container Platform.

## <a name="openshift-origin"></a>OpenShift Origin

Projet initial [open source](https://www.openshift.org/) d’OpenShift dont le support est assuré par la communauté. Origin peut être installé sur CentOS ou RHEL.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Version professionnelle ([offre commerciale](https://www.openshift.com)) de Red Hat qui en assure le support. Le client achète les droits nécessaires pour OpenShift Container Platform et est responsable de l’installation et de la gestion de l’ensemble de l’infrastructure.

Étant donné que le client est « propriétaire » de l’ensemble de la plateforme, il peut l’installer dans son centre de données local, sur un cloud public (Azure, AWS, Google, etc.), etc.

## <a name="openshift-online"></a>OpenShift Online

Version d’OpenShift **mutualisée** gérée par Red Hat (à l’aide de Container Platform). Red Hat gère toute l’infrastructure sous-jacente (machines virtuelles, cluster OpenShift, mise en réseau, stockage, etc.). 

Le client déploie des conteneurs, mais n’a aucun contrôle sur les hôtes qu’ils exécutent. Dans la mesure où il s’agit d’une architecture mutualisée, les conteneurs peuvent coexister sur les mêmes hôtes de machine virtuelle que les conteneurs d’autres clients. Le coût est fixé par conteneur.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Version d’OpenShift **à client unique** gérée par Red Hat (à l’aide de Container Platform). Red Hat gère toute l’infrastructure sous-jacente (machines virtuelles, cluster OpenShift, mise en réseau, stockage, etc.). Le cluster est propre à un client et est exécuté dans un cloud public (AWS, Google, Azure - disponible début 2018). Le cluster de base inclut quatre nœuds d’application pour 48 000 $/an (paiement initial pour une année entière).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les éléments prérequis communs pour OpenShift dans Azure](./openshift-prerequisites.md)
- [Déployer OpenShift Origin](./openshift-origin.md)
- [Déployer OpenShift Container Platform](./openshift-container-platform.md)
- [Tâches post-déploiement](./openshift-post-deployment.md)
- [Résolution des problèmes liés au déploiement d’OpenShift](./openshift-troubleshooting.md)
