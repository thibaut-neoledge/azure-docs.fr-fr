---
title: Surveiller le cluster Azure Kubernetes avec DataDog | Microsoft Docs
description: "Surveillance du cluster Kubernetes dans Azure Container Service à l’aide de DataDog"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: what-goes-here?
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 2984a3811eba670b54fd253a8d7dc84acd8c8ec4


---

# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Surveiller un cluster Azure Container Service avec Datadog

## <a name="prerequisites"></a>Composants requis
Cette procédure pas à pas suppose que vous avez [créé un cluster Kubernetes à l’aide d’Azure Container Service](container-service-kubernetes-walkthrough.md).

Elle suppose également que vous avez installé l’outil `az` de l’interface Azure CLI et l’outil `kubectl`.

Vous pouvez tester si l’outil `az` est installé en exécutant :

```console
$ az --version
```

Si l’outil `az` n’est pas installé, suivez les instructions figurant [ici](https://github.com/azure/azure-cli#installation).

Vous pouvez tester si l’outil `kubectl` est installé en exécutant :

```console
$ kubectl version
```

Si `kubectl` n’est pas installé, vous pouvez exécuter :

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>DataDog
Datadog est un service de surveillance qui regroupe les données de surveillance provenant de vos conteneurs dans votre cluster Azure Container Service. Datadog intègre un tableau de bord Docker Integration qui affiche des mesures spécifiques dans vos conteneurs. Les mesures recueillies à partir de vos conteneurs sont classées par processeur, mémoire, réseau et E/S. Datadog fractionne les mesures en conteneurs et images.

Vous devez d’abord [créer un compte](https://www.datadoghq.com/lpg/).

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Installation de DataDog Agent avec un DaemonSet
Kubernetes utilise les DaemonSet pour exécuter une instance unique d’un conteneur sur chaque hôte du cluster.
Ils sont idéaux pour exécuter des agents de surveillance.

Lorsque vous êtes connecté à DataDog, suivez les [instructions de DataDog](https://app.datadoghq.com/account/settings#agent/kubernetes) pour installer des agents DataDog sur votre cluster à l’aide d’un DaemonSet.

## <a name="conclusion"></a>Conclusion
Et voilà ! Dès que les agents sont en cours d’exécution, des données s’affichent dans la console après quelques minutes. Vous pouvez consulter le [tableau de bord intégré de Kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) pour obtenir une synthèse de votre cluster.


<!--HONumber=Jan17_HO4-->


