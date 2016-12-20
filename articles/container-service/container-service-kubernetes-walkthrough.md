---
title: "Déployer un cluster Azure Container Service avec Kubernetes | Microsoft Docs"
description: "Déploiement d’un cluster Azure Container Service avec Kubernetes"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 0989c85e4b7c73a8b7b335254e4af90ef34ddc01
ms.openlocfilehash: 8c10da9d2363f0938d821b59cc4fd58b53f9ed17


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Moteur de Microsoft Azure Container Service - Procédure pas à pas Kubernetes

## <a name="deployment"></a>Déploiement

Voici les étapes permettant de déployer un cluster Kubernetes simple :

1. [générer votre clé ssh](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#ssh-key-generation)
3. [générer votre principal du service](https://github.com/Azure/acs-engine/blob/master/docs/serviceprincipal.md)
4. Cliquez sur le [bouton Déployer sur Azure sur LISEZ-MOI](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-kubernetes/README.md) et renseignez les champs

## <a name="walkthrough"></a>Procédure pas à pas

Une fois votre cluster Kubernetes créé, vous disposez d’un groupe de ressources contenant :

1. 1 serveur maître accessible par SSH sur le port 22 ou kubectl sur le port 443

2. un ensemble de nœuds dans un groupe à haute disponibilité.  Les nœuds sont accessibles via un serveur maître.  Consultez [Transfert de l’agent](https://github.com/Azure/azure-quickstart-templates/blob/master/101-acs-dcos/docs/SSHKeyManagement.md#key-management-and-agent-forwarding-with-windows-pageant) pour obtenir un exemple indiquant comment procéder.

L’image suivante montre l’architecture d’un cluster de service de conteneur comportant 1 maître et 2 agents :

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-walkthrough/kubernetes.png)

Dans l’image ci-dessus, vous pouvez voir les éléments suivants :

1. **Les composants du serveur maître** : le serveur maître exécute le planificateur de Kubernetes, le serveur d’API et le gestionnaire de contrôleurs.  Le port 443 est exposé pour la gestion à distance avec l’interface de ligne de commande kubectl.
2. **Les nœuds** : les nœuds Kubernetes sont exécutés dans un groupe à haute disponibilité.  Les équilibreurs de charge Azure sont ajoutés dynamiquement au cluster en fonction des services exposés. 
3. **Les composants communs** : toutes les machines virtuelles exécutant un kubelet, un Docker et un Proxy.
4. **La mise en réseau** : toutes les machines virtuelles disposent d’une adresse IP affectée dans le réseau 10.240.0.0/16.  Chaque machine virtuelle est associée à un sous-réseau /24 pour son bloc CIDR activant l’IP par bloc.  Le proxy en cours d’exécution sur chaque machine virtuelle implémente le réseau de service 10.0.0.0/16.

Toutes les machines virtuelles figurent dans le même réseau privé virtuel et sont entièrement accessibles les unes par rapport aux autres.

## <a name="create-your-first-kubernetes-service"></a>Créer votre premier service Kubernetes

Une fois cette procédure terminée, vous saurez comment :
 * accéder au cluster Kubernetes via SSH ;
 * déployer une application simple de Docker et l’exposer au public ;
 * identifier l’emplacement du fichier de configuration Kube et comment accéder à distance au cluster Kubernetes ;
 * utiliser `kubectl exec` pour exécuter des commandes dans un conteneur ; 
 * et enfin comment accéder au tableau de bord Kubernetes.

1. Après avoir correctement déployé le modèle, notez les noms de domaine complets du serveur maître.
   1. Si vous utilisez PowerShell ou l’interface CLI, le paramètre de sortie est situé dans la section OutputsString nommée « masterFQDN ».
   2. Si vous utilisez le portail, accédez au panneau de vue d’ensemble de la ressource ContainerService pour copier la valeur « Master FQDN » :
     
   ![Image de mise à l’échelle de docker](media/container-service-kubernetes-walkthrough/portal-kubernetes-outputs.png)

2. SSH pour le nom de domaine complet du serveur maître obtenu à l’étape 1.

3. Explorez vos nœuds et les blocs en cours d’exécution :
  1. Pour afficher la liste de vos nœuds de type `kubectl get nodes`.  Si vous souhaitez obtenir tous les détails sur les nœuds, ajoutez `-o yaml` pour qu’il devienne `kubectl get nodes -o yaml`.
  2. pour afficher la liste des blocs en cours d’exécution de type `kubectl get pods --all-namespaces`.

4. Démarrez votre première image Docker en tapant `kubectl run nginx --image nginx`.  Cette opération démarrera le conteneur Docker nginx dans un bloc sur l’un des nœuds.

5. Tapez `kubectl get pods -o yaml` pour afficher des informations complètes sur le déploiement de nginx. Vous pouvez voir l’adresse IP hôte et l’IP du bloc.  L’adresse IP du bloc est affectée à partir du bloc CIDR sur l’hôte.  Exécutez la commande curl sur l’adresse IP du bloc pour afficher la sortie de nginx, par exemple. `curl 10.244.1.4`

  ![Image de curl sur l’adresse IP du bloc](media/container-service-kubernetes-walkthrough/kubernetes-nginx1.png)

6. L’étape suivante consiste à exposer le déploiement nginx en tant que service Kubernetes sur le réseau de service privé 10.0.0.0/16 :
  1. exposer le service avec la commande `kubectl expose deployment nginx --port=80`.
  2. obtenir l’adresse IP de service `kubectl get service`
  3. exécuter la commande curl sur l’adresse IP, par exemple. `curl 10.0.105.199`

  ![Image de curl sur l’adresse IP du service](media/container-service-kubernetes-walkthrough/kubernetes-nginx2.png)

7. L’étape finale consiste à rendre le service accessible au public.  Pour y parvenir, remplacez le type de service `ClusterIP` par `LoadBalancer` :
  1. modifiez le service : `kubectl edit svc/nginx`
  2. modifiez `type` en remplaçant `ClusterIP` par `LoadBalancer` et enregistrez.  Kubernetes créera ainsi un équilibrage de charge Azure avec une adresse IP publique.
  3. Cette modification prendra environ 2 à 3 minutes.  Observez la modification du service de « en attente » à un type d’adresse IP externe `watch 'kubectl get svc'`

  ![Image de la transition du statut « en attente » vers « adresse IP externe »](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

  4. Une fois que vous voyez l’adresse IP externe, vous pouvez y accéder dans votre navigateur :

  ![Image de la navigation vers nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  

8. L’étape suivante de cette procédure pas à pas consiste à vous montrer comment gérer à distance votre cluster Kubernetes.  Téléchargez tout d’abord Kubectl sur votre ordinateur et placez-le dans votre chemin d’accès :
  * [Windows Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/windows/amd64/kubectl.exe)
  * [OSX Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/darwin/amd64/kubectl)
  * [Linux](https://storage.googleapis.com/kubernetes-release/release/v1.4.5/bin/linux/amd64/kubectl)

9. Le serveur maître de Kubernetes contient le fichier de configuration kube pour l’accès à distance sous le répertoire de base ~/.kube/config.  Téléchargez ce fichier sur votre ordinateur, définissez la variable d’environnement KUBECONFIG et exécutez kubectl pour vérifier que vous pouvez vous connecter au cluster :
  * Windows utilise la commande pscp de [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  Assurez-vous que votre certificat est exposé via [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant) :
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```
  * OS X ou Linux :
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
10. L’étape suivante consiste à vous montrer comment exécuter des commandes à distance dans un conteneur Docker à distance :
  1. Exécutez `kubectl get pods` pour afficher le nom de votre bloc nginx
  2. à l’aide de votre nom de bloc, vous pouvez exécuter une commande à distance sur votre bloc.  par exemple `kubectl exec nginx-701339712-retbj date`
  3. Essayez d’exécuter une session de bash à distance. par exemple `kubectl exec nginx-701339712-retbj -it bash`.  La capture d’écran suivante illustre ces commandes :

  ![Image de curl sur l’adresse IP du bloc](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)

11. La dernière étape de ce didacticiel consiste à vous présenter le tableau de bord :
  1. Exécutez `kubectl proxy` pour vous connecter directement au proxy.
  2. Dans votre navigateur, accédez au [tableau de bord](http://127.0.0.1:8001/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard/#/workload?namespace=_all).
  3. Parcourez et explorez vos services et vos blocs.
  ![Image du tableau de bord Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

# <a name="learning-more"></a>En savoir plus

Voici quelques liens recommandés pour en savoir plus sur Kubernetes :

1. [Documentation sur Azure Kubernetes](https://azure.microsoft.com/en-us/documentation/services/container-service/)

## <a name="kubernetes-community-documentation"></a>Documentation de la communauté Kubernetes

1. [Camp d’entraînement Kubernetes](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) : montre comment déployer, mettre à l’échelle, mettre à jour et déboguer des applications.
2. [Guide d’utilisateur Kubernetes](http://kubernetes.io/docs/user-guide/) : fournit des informations sur les programmes en cours d’exécution dans un cluster Kubernetes existant.
3. [Exemples de Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) : fournit des exemples d’exécution d’applications réelles avec Kubernetes.



<!--HONumber=Nov16_HO3-->


