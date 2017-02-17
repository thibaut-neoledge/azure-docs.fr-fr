---
title: "Gérer le cluster Kubernetes Azure avec l’interface utilisateur Web | Microsoft Docs"
description: "Utilisation de l’interface web Kubernetes dans Azure Container Service"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 4192369f2e7758200131aa85c60d07436f7cbbdc


---

# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Utilisation de l’interface utilisateur Web Kubernetes avec Azure Container Service

## <a name="prerequisites"></a>Composants requis
Cette procédure pas à pas suppose que vous avez [créé un cluster Kubernetes à l’aide d’Azure Container Service](container-service-kubernetes-walkthrough.md).

> [!NOTE]
> La prise en charge de Kubernetes dans l’Azure Container Service est actuellement en version préliminaire.
>

Elle suppose également que vous avez installé les outils azure cli et kubectl.

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

## <a name="overview"></a>Vue d'ensemble

### <a name="connect-to-the-web-ui"></a>Connexion à l’interface web
Vous pouvez lancer l’interface web Kubernetes en exécutant :

```console
$ az acs kubernetes browse
```

Cette commande ouvre un navigateur web configuré pour communiquer avec un proxy sécurisé en connectant votre machine locale à l’interface web Kubernetes.

### <a name="create-and-expose-a-service"></a>Création et exposition d’un service
Dans l’interface utilisateur web Kubernetes, vous devez voir un bouton « Créer » dans la fenêtre supérieure droite.

![Interface de création Kubernetes](media/k8s/create.png)

Cette option permet d’ouvrir une boîte de dialogue dans laquelle vous pouvez commencer à créer votre application.
Donnez-lui le nom `hello-nginx`. Utilisez le [ `nginx` conteneur de Docker](https://hub.docker.com/_/nginx/) et déployez trois réplicas de ce service web.

![Boîte de dialogue de création de pods Kubernetes](media/k8s/nginx.png)

Créez ensuite un service Kubernetes « Externe » pour équilibrer la charge de trafic sur nos trois réplicas.  Sélectionnez « Externe » et entrez le port 80.

![Boîte de dialogue de création de service Kubernetes](media/k8s/service.png)

Enfin, appuyez sur le bouton « Déployer » pour déployer ces conteneurs et ces services.

![Déploiement de Kubernetes](media/k8s/deploy.png)

### <a name="view-your-containers"></a>Affichage de vos conteneurs
Une fois que vous appuyez sur « Déployer », l’interface utilisateur affiche une vue de votre service pendant son déploiement :

![État de Kubernetes](media/k8s/status.png)

Vous pouvez voir l’état de chaque objet Kubernetes dans le cercle sur le côté gauche de l’interface utilisateur. Si le cercle est partiellement complet, l’objet est toujours en cours de déploiement. Lorsqu’un objet est entièrement déployé, une coche verte s’affiche :

![Kubernetes déployé](media/k8s/deployed.png)

Une fois que tout est en cours d’exécution, vous pouvez cliquer sur l’un de vos pods pour afficher les détails sur le service web exécuté

![Pods kubernetes](media/k8s/pods.png)

Dans la vue Pod spécifique, vous pouvez voir des informations sur les conteneurs dans le pod, ainsi que les ressources de processeur et de mémoire utilisées par ces conteneurs :

![Ressources Kubernetes](media/k8s/resources.png)

Si vous ne voyez pas les ressources, vous devrez peut-être attendre quelques minutes que les données de surveillance se propagent.

Vous pouvez également cliquer sur le lien « Journaux » pour afficher les journaux de votre conteneur :

![Journaux Kubernetes](media/k8s/logs.png)

### <a name="viewing-your-service"></a>Affichage de votre service
Outre l’exécution de vos conteneurs, l’interface Kubernetes a créé un élément `Service` externe qui approvisionne un équilibrage de charge pour diriger le trafic vers les conteneurs de votre cluster.

Vous pouvez cliquer sur « Services » dans le volet de navigation de gauche pour afficher tous les services (pour le moment, vous ne devriez en voir qu’un)

![Services Kubernetes](media/k8s/service-deployed.png)

Dans cette vue, vous devriez être en mesure de voir une adresse IP externe qui a été affectée à votre service.
Si vous cliquez sur cette adresse IP, vous devez voir votre conteneur nginx en cours d’exécution derrière l’équilibrage de charge.

![Vue nginx](media/k8s/nginx-page.png)

### <a name="resizing-your-service"></a>Redimensionnement de votre service
Outre l’affichage de vos objets dans l’interface, vous pouvez également modifier et mettre à jour les objets API Kubernetes.

Tout d’abord, accédez à `Deployment` dans votre service en cliquant sur les « Déploiements » dans le volet de navigation gauche.

Une fois que vous êtes dans cette vue, cliquez sur ReplicaSet, puis sur le bouton « Modifier » dans la barre de navigation supérieure :

![Modification de Kubernetes](media/k8s/edit.png)

Définissez le champ `spec.replicas` sur «&2; », puis appuyez sur « Mettre à jour ».

Il n’y aura donc plus que deux réplicas, car l’un de vos pods sera supprimé.

 




<!--HONumber=Jan17_HO4-->


