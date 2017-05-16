---
title: Cluster Azure Kubernetes pour Windows | Microsoft Docs
description: "Déploiement et prise en main d’un cluster Kubernetes pour des conteneurs Windows dans Azure Container Service"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: fr-fr
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Prise en main des conteneurs Kubernetes et Windows dans Container Service


Cet article décrit comment créer dans Azure Container Service un cluster Kubernetes qui contient des nœuds Windows pour exécuter des conteneurs Windows. Commencez par utiliser les commandes `az acs` Azure CLI 2.0 pour créer le cluster Kubernetes dans Azure Container Service. Ensuite, utilisez l’outil de ligne de commande Kubernetes `kubectl` pour commencer à travailler avec les conteneurs Windows construits à partir d’images Docker. 

> [!NOTE]
> Le support des conteneurs Windows avec Kubernetes est en version préliminaire. 
>



L’illustration suivante montre l’architecture de cluster Kubernetes dans Azure Container Service avec un seul nœud maître Linux et deux nœuds d’agent Windows. 

![Image du cluster Kubernetes sur Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* Le maître Linux fait office d’API REST Kubernetes et est accessible par SSH sur le port 22 ou `kubectl` sur le port 443. 
* Les nœuds d’agent Windows sont rassemblés dans un groupe à haute disponibilité Azure et exécutent vos conteneurs. Les nœuds Windows sont accessibles par le biais d’un tunnel SSH RDP via le nœud principal. Les règles d’équilibreurs de charge Azure sont ajoutées dynamiquement au cluster en fonction des services exposés.



Toutes les machines virtuelles figurent dans le même réseau privé virtuel et sont entièrement accessibles les unes par rapport aux autres. Toutes les machines virtuelles exécutant un kubelet, un Docker et un Proxy.

Pour plus d’informations, consultez la [présentation d’Azure Container Service](container-service-intro.md) et la [documentation de Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Composants requis
Pour créer un cluster Azure Container Service à l’aide d’Azure CLI 2.0, vous devez :
* disposer d’un compte Azure ([obtenir une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/))
* avoir installé et ouvert une session [Azure CLI 2.0](/cli/azure/install-az-cli2).

Vous avez également besoin des éléments suivants pour votre cluster Kubernetes. Vous pouvez les préparer à l’avance ou utiliser les options de commande `az acs create` pour les générer automatiquement pendant le déploiement du cluster. 

* **Clé publique RSA SSH** : pour créer des clés RSA SSH (Secure Shell), consultez les conseils sur [macOS et Linux](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **Secret et ID client du principal de service** : pour plus d’informations sur la création d’un principal de service Azure Active Directory, consultez [À propos du principal de service d’un cluster Kubernetes](container-service-kubernetes-service-principal.md).

Dans cet article, l’exemple de commande génère automatiquement les clés SSH et un principal de service.
  
## <a name="create-your-kubernetes-cluster"></a>Créer votre cluster Kubernetes

Voici les commandes Azure CLI 2.0 permettant de créer votre cluster. 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un groupe de ressources dans un emplacement où Azure Container Service est [disponible](https://azure.microsoft.com/regions/services/). La commande suivante crée le groupe de ressources *myKubernetesResourceGroup* à l’emplacement *westus* :

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Créer un cluster Kubernetes avec des nœuds d’agent Windows

Créez un cluster Kubernetes dans votre groupe de ressources à l’aide de la commande `az acs create` avec `--orchestrator-type=kubernetes` et de l’option d’agent `--windows`. Pour la syntaxe de commande, consultez l’[aide](/cli/azure/acs#create) de `az acs create`.

La commande suivante crée le cluster Container Service *myKubernetesClusterName*, avec un préfixe DNS *myPrefix* du nœud de gestion et les informations d’identification spécifiées pour atteindre les nœuds Windows. Cette version de la commande génère automatiquement les clés RSA SSH et le principal de service du cluster Kubernetes.


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

Après quelques minutes, la commande termine son exécution. Vous devez alors disposer d’un cluster Kubernetes opérationnel.

> [!IMPORTANT]
> Si votre compte ne dispose pas des autorisations pour créer le principal de service Azure AD, la commande génère une erreur similaire à `Insufficient privileges to complete the operation.`. Pour plus d’informations, consultez [À propos du principal de service d’un cluster Kubernetes](container-service-kubernetes-service-principal.md). 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>Se connecter au cluster avec kubectl

Pour vous connecter au cluster Kubernetes depuis l’ordinateur client, utilisez l’outil [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), le client de ligne de commande Kubernetes. 

Si vous n’avez pas encore installé `kubectl` localement, vous pouvez le faire avec `az acs kubernetes install-cli`. (Vous pouvez également le télécharger à partir du [site Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/).)

**Linux ou macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> Par défaut, cette commande installe le fichier binaire de `kubectl` dans le répertoire `/usr/local/bin/kubectl` sur un système Linux ou macOS, ou dans `C:\Program Files (x86)\kubectl.exe` sous Windows. Pour spécifier un autre chemin d’installation, utilisez le paramètre `--install-location`.
>
> Une fois `kubectl` installé, vérifiez que son répertoire figure dans le chemin d’accès de votre système. Le cas échéant, ajoutez-le. 


Ensuite, exécutez la commande suivante pour télécharger la configuration du cluster Kubernetes maître dans le fichier `~/.kube/config` local :

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

À ce stade, vous être prêt à accéder à votre cluster depuis votre ordinateur. Essayez d’exécuter :

```bash
kubectl get nodes
```

Puis vérifiez que vous pouvez voir les machines dans votre cluster.

![Nœuds d’un cluster Kubernetes](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>Création de votre premier service Kubernetes

Après avoir créé le cluster et vous y être connecté avec `kubectl`, essayez de démarrer une application Windows à partir d’un conteneur Docker et rendez-la accessible depuis Internet. Cet exemple de base utilise un fichier JSON pour spécifier un conteneur Microsoft Internet Information Server (IIS), puis le crée à l’aide de `kubctl apply`. 

1. Créez un fichier local nommé `iis.json` et copiez le contenu suivant. Ce fichier demande à Kubernetes d’exécuter IIS sur Windows Server 2016 Server Core, à l’aide d’une image publique provenant de [Docker Hub](https://hub.docker.com/r/microsoft/iis/). Le conteneur utilise le port 80, mais au début il n’est accessible que dans le réseau du cluster.

  ```JSON
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "iis",
      "labels": {
        "name": "iis"
      }
    },
    "spec": {
      "containers": [
        {
          "name": "iis",
          "image": "microsoft/iis",
          "ports": [
            {
            "containerPort": 80
            }
          ]
        }
      ],
      "nodeSelector": {
        "beta.kubernetes.io/os": "windows"
      }
    }
  }
  ```
2. Pour lancer l’application, saisissez :  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. Pour suivre le déploiement du conteneur, tapez :  
  ```bash
  kubectl get pods
  ```
  Bien que le conteneur soit en cours de déploiement, l’état est `ContainerCreating`. 

  ![Conteneur IIS dans l’état ContainerCreating](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  Selon la taille de l’image IIS, il peut falloir plusieurs minutes au conteneur pour qu’il prenne l’état `Running`.

  ![Conteneur IIS dans l’état Running](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. Pour rendre le conteneur accessible au monde, tapez la commande suivante :

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  Avec cette commande, Kubernetes crée une règle Azure Load Balancer avec une adresse IP publique. La propagation de cette modification de l’équilibreur de charge dure quelques minutes. Pour plus d’informations, consultez [Équilibrer la charge des conteneurs dans un cluster Kubernetes dans Azure Container Service](container-service-kubernetes-load-balancing.md).

5. Exécutez la commande suivante pour afficher l’état du service.

  ```bash
  kubectl get svc
  ```

  L’adresse IP affichée initialement apparaît en tant que `pending` :

  ![Adresse IP externe en attente](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  Après quelques minutes, l’adresse IP est définie :
  
  ![Adresse IP externe d’IIS](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. Une fois l’adresse IP externe disponible, vous pouvez y accéder dans votre navigateur :

  ![Image de la navigation vers IIS](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. Pour supprimer le pod IIS, tapez :

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser l’interface utilisateur de Kubernetes, exécutez la commande `kubectl proxy`. Ensuite, accédez à http://localhost:8001/ui.

* Pour savoir comment créer un site web IIS personnalisé et l’exécuter dans un conteneur Windows, consultez les informations disponibles dans [Docker Hub](https://hub.docker.com/r/microsoft/iis/).

* Pour accéder aux nœuds Windows via un tunnel SSH RDP relié au maître avec PuTTy, consultez la [documentation sur ACS-Engine](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master). 

