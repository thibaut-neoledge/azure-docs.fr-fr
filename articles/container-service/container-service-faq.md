---
title: Azure Container Service - Forum Aux Questions | Microsoft Docs
description: "Réponses aux questions fréquemment posées sur Azure Container Service, un service qui simplifie la création, la configuration et la gestion d’un cluster de machines virtuelles pour exécuter des applications de conteneur Docker."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Mesos, Azure, Kubernetes
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 3bb83f231d16819e5f5da7edbc9fc3f38baff011
ms.openlocfilehash: b0c5efa595b0377d7ae2d936d0394667356d18c9


---
# <a name="frequently-asked-questions-azure-container-service"></a>Forum aux questions : Azure Container Service


## <a name="orchestrators"></a>Orchestrators

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Quels orchestrators de conteneur sont pris en charge sur Azure Container Service ? 

Une prise en charge est disponible pour CD/OS open source, Docker Swarm et Kubernetes. La prise en charge de DC/OS et de Docker Swarm est généralement disponible, la prise en charge de Kubernetes est actuellement en version préliminaire. Pour plus d'informations, consultez la [Vue d’ensemble](container-service-intro.md).
 
### <a name="do-you-support-swarm-mode"></a>Le mode Swarm est-il pris en charge ? 

Le mode Swarm n’est actuellement pas pris en charge, mais il figure sur la feuille de route du service. 

### <a name="does-azure-container-service-support-windows-containers"></a>Azure Container Service prend-il en charge les conteneurs Windows ?  

Les conteneurs Linux sont actuellement pris en charge. La prise en charge des conteneurs Windows avec les orchestrators DC/OS, Docker Swarm et Kubernetes figure sur la feuille de route du service. 

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Recommandez-vous un orchestrator spécifique dans Azure Container Service ? 
Nous ne recommandons généralement pas un orchestrator spécifique. Si vous êtes habitué avec un des orchestrators pris en charge, vous pouvez profiter de cette expérience dans Azure Container Service. Les tendances des données suggère toutefois que DC/OS est éprouvé en production pour les charges de travail Big Data et IoT, que Kubernetes est adapté aux charges de travail natif de cloud, et que Docker Swarm est connu pour son intégration aux outils Docker et sa simplicité de prise en main.

En fonction de votre scénario, vous pouvez également créer et gérer des solutions de conteneur personnalisé avec d’autres services Azure. Ces services incluent les [machines virtuelles](../virtual-machines/virtual-machines-linux-azure-overview.md), [Service Fabric](../service-fabric/service-fabric-overview.md), [Web Apps](../app-service-web/app-service-web-overview.md) et [lot](../batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Quelle est la différence entre Azure Container Service et ACS Engine ? 
Azure Container Service est un service Azure reposant sur un SLA qui inclut des fonctionnalités dans le portail Azure, des outils de ligne de commande Azure et des API Azure. Le service vous permet de mettre en œuvre et de gérer rapidement des clusters exécutant des outils d’orchestration de conteneur standard avec un nombre de possibilités de configuration relativement faible. 

[ACS Engine](http://github.com/Azure/acs-engine) est un projet open source qui permet aux utilisateurs de personnaliser la configuration du cluster à tous les niveaux. Cette possibilité de modifier la configuration de l’infrastructure et des logiciels implique que nous ne proposons aucun SLA pour l’ACS Engine. La prise en charge est gérée via le projet open source sur GitHub plutôt que via des canaux Microsoft officiels. 

## <a name="cluster-management"></a>Gestion de cluster

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Comment créer des clés SSH pour mon cluster ?

Vous pouvez utiliser des outils standard sur votre système d’exploitation pour créer une paire de clés publique et privée RSA SSH pour l’authentification sur les machines virtuelles Linux de votre cluster. Pour la procédure, consultez les conseils pour [OS X et Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

Si vous utilisez des [commandes Azure CLI 2.0 (version préliminaire)](container-service-create-acs-cluster-cli.md) pour déployer un cluster de service de conteneur, des clés SSH peuvent être générés automatiquement pour votre cluster.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Comment créer un principal du service pour mon cluster Kubernetes ?

Un ID et un mot de passe de principal du service Azure Active Directory sont également nécessaires pour créer un cluster Kubernetes dans Azure Container Service. Pour en savoir plus, consultez [À propos du principal du service pour un cluster Kubernetes](container-service-kubernetes-service-principal.md)


Si vous utilisez des [commandes Azure CLI 2.0 (version préliminaire)](container-service-create-acs-cluster-cli.md) pour déployer un cluster Kubernetes, des informations d’identification du principal du service peuvent être générées automatiquement pour votre cluster.


### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Comment augmenter le nombre de maîtres après la création d’un cluster ? 
Une fois que le cluster est créé, le nombre de maîtres est fixe et ne peut pas être modifié. Lors de la création du cluster, vous devez idéalement sélectionner trois ou cinq maîtres pour la haute disponibilité.

> [!NOTE]
> Dans la version préliminaire, un cluster Kubernetes dans Azure Container Service ne peut avoir qu’un seul maître.
>

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Comment augmenter le nombre d’agents après la création d’un cluster ? 
Vous pouvez faire évoluer le nombre d’agents dans le cluster à l’aide du portail Azure ou d’outils de ligne de commande. Consultez [Mise à l’échelle d’un cluster Azure Container Service](container-service-scale.md).

> [!NOTE]
> Dans la version préliminaire, un cluster Kubernetes dans Azure Container Service comporte un nombre fixe d’agents. 
>

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Quelles sont les URL de mes maîtres et agents ? 
Les URL des ressources du cluster dans Azure Container Service sont basées sur le préfixe du nom DNS que vous indiquez et le nom de la région Azure que choisissez pour le déploiement. Par exemple, le nom de domaine complet (FQDN) du nœud principal se présente sous la forme suivante :

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Vous pouvez retrouver des URL fréquemment utilisées pour votre cluster dans le portail Azure, Azure Resource Explorer ou d’autres outils Azure.
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Où trouver la chaîne de connexion SSH à mon cluster ?

Vous pouvez retrouver la chaîne de connexion dans le portail Azure ou à l’aide des outils de ligne de commande Azure. 

1. Dans le portail, accédez au groupe de ressources pour le déploiement du cluster.  

2. Cliquez sur **Vue d’ensemble** et cliquez sur le lien **Déploiements** sous **Bases**. 

3. Dans le panneau **Historique des déploiements**, cliquez sur le déploiement dont le nom commence par **microsoft-acs**, suivi d’une date de déploiement. Exemple : microsoft-acs-201701310000.  

4. Dans la page **Résumé**, sous **Sorties**, plusieurs liens de cluster sont présentés <provided></provided>. **SSHMaster0** fournit une chaîne de connexion SSH au premier maître dans votre cluster de service du conteneur. 

Comme indiqué précédemment, vous pouvez également utiliser des outils Azure pour rechercher le nom de domaine complet (FQDN) du maître. Établissez une connexion SSH au maître à l’aide du nom de domaine complet (FQDN) du maître et du nom d’utilisateur que vous avez spécifié lors de la création du cluster. Par exemple :

```bash
ssh userName@masterFQDN –A –p 22 
```

Pour plus d’informations, consultez [Connexion à un cluster Azure Container Service](container-service-connect.md).




## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus](container-service-intro.md) sur Azure Container Service.
* Déployez un cluster de service de conteneur à l’aide du [portail](container-service-deployment.md) ou d’[Azure CLI 2.0 (version préliminaire)](container-service-create-acs-cluster-cli.md).


<!--HONumber=Feb17_HO3-->


