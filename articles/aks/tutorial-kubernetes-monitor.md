---
title: Didacticiel Kubernetes sur Azure - Surveiller Kubernetes
description: "Didacticiel AKS - Surveiller Kubernetes à l’aide de Microsoft Operations Management Suite (OMS)"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a41f699291a65129906680cbb6719c2478c0d830
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2017
---
# <a name="monitor-azure-container-service-aks"></a>Surveillance d’Azure Container Service (AKS)

La surveillance de votre cluster Kubernetes et des conteneurs est cruciale, particulièrement lorsque vous exécutez un cluster de production à grande échelle avec plusieurs applications.

Dans ce didacticiel, vous allez configurer la surveillance de votre cluster AKS à l’aide de la [solution de conteneurs pour Log Analytics](../log-analytics/log-analytics-containers.md).

Dans ce didacticiel (le septième d’une série de huit), les tâches suivantes sont abordées :

> [!div class="checklist"]
> * Configuration de la solution de surveillance de conteneurs
> * Configuration des agents de surveillance
> * Accès aux informations de surveillance sur le portail Azure

## <a name="before-you-begin"></a>Avant de commencer

Dans les didacticiels précédents, une application a été empaquetée dans des images conteneur, ces images chargées sur Azure Container Registry et un cluster Kubernetes créé.

Si vous n’avez pas accompli ces étapes et que vous souhaitez suivre cette procédure, revenez au [Didacticiel 1 – Créer des images conteneur](./tutorial-kubernetes-prepare-app.md).

## <a name="configure-the-monitoring-solution"></a>Configurer la solution de surveillance

Dans le portail Azure, sélectionnez **Nouveau** et recherchez `Container Monitoring Solution`. Une fois l’élément localisé, sélectionnez **Créer**.

![Ajouter une solution](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Sélectionnez ou créez un espace de travail OMS. Le formulaire Espace de travail OMS vous explique la procédure à suivre.

Lorsque vous créez un espace de travail, sélectionnez **Épingler au tableau de bord** pour le retrouver plus facilement.

![Espace de travail OMS](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Lorsque vous avez terminé, sélectionnez **OK**. Une fois la validation terminée, sélectionnez **Créer** pour créer la solution de surveillance de conteneurs.

Une fois l’espace de travail créé, celui-ci s’affiche dans le portail Azure.

## <a name="get-workspace-settings"></a>Obtenir les paramètres de l’espace de travail

Vous aurez besoin de la clé et de l’ID de l’espace de travail Log Analytics pour configurer l’agent de la solution sur les nœuds Kubernetes.

Pour récupérer ces valeurs, sélectionnez **Espace de travail OMS** dans le menu de gauche de la solution de conteneurs. Sélectionnez **Paramètres avancés** et notez **l’ID de l’espace de travail** et la **clé primaire**.

## <a name="configure-monitoring-agents"></a>Configurer les agents de surveillance

Le fichier manifeste Kubernetes suivant peut être utilisé pour configurer les agents de surveillance des conteneurs sur un cluster Kubernetes. Il crée un [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes qui exécute un pod unique sur chaque nœud du cluster.

Enregistrez le texte suivant dans un fichier nommé `oms-daemonset.yaml` et remplacez les valeurs d’espace réservé de `WSID` et `KEY` par vos ID et clé d’espace de travail Log Analytics.

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
        - mountPath: /var/log
          name: host-log
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: container-hostname
      hostPath:
       path: /etc/hostname
    - name: host-log
      hostPath:
       path: /var/log
```

Créer le DaemonSet à l’aide de la commande suivante :

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Pour vérifier que le DaemonSet est créé, exécutez :

```azurecli-interactive
kubectl get daemonset
```

Le résultat ressemble à ce qui suit :

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Dès lors que les agents sont en cours d’exécution, quelques minutes sont nécessaires à OMS pour ingérer et traiter les données.

## <a name="access-monitoring-data"></a>Accéder aux données de surveillance

Dans le portail Azure, sélectionnez l’espace de travail Log Analytics qui a été épinglé au tableau de bord du portail. Cliquez sur la vignette **Solution de surveillance de conteneurs**. Vous trouverez ici des informations sur le cluster AKS et les conteneurs associés.

![tableau de bord](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Consultez la [documentation Azure Log Analytics](../log-analytics/index.yml) pour obtenir des instructions détaillées sur l’interrogation et l’analyse des données de surveillance.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous surveillez votre cluster Kubernetes avec OMS. Les tâches traitées ont inclus :

> [!div class="checklist"]
> * Configuration de la solution de surveillance de conteneurs
> * Configuration des agents de surveillance
> * Accès aux informations de surveillance sur le portail Azure

Passez au didacticiel suivant pour en savoir plus sur la mise à niveau de Kubernetes vers une nouvelle version.

> [!div class="nextstepaction"]
> [Mettre à niveau Kubernetes](./tutorial-kubernetes-upgrade-cluster.md)