---
title: "Équilibrer la charge des conteneurs Kubernetes dans un cluster Azure | Microsoft Docs"
description: "Connectez-vous en externe et équilibrez la charge sur plusieurs conteneurs dans un cluster Kubernetes dans Azure Container Service."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Conteneurs, micro-services, Kubernetes, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: danlep
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: e2ac0d65eea9cea25a2a9cb1f99a35c689eed976
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---
# <a name="load-balance-containers-in-a-kubernetes-cluster-in-azure-container-service"></a>Équilibrer la charge des conteneurs dans un cluster Kubernetes dans Azure Container Service 
Cet article présente l’équilibrage de charge dans un cluster Kubernetes dans Azure Container Service. L’équilibrage de charge fournit une adresse IP accessible en externe pour le service et distribue le trafic réseau entre les blocs en cours d’exécution dans les machines virtuelles de l’agent.

Vous pouvez configurer un service Kubernetes pour utiliser l’[équilibrage de charge Azure](../../load-balancer/load-balancer-overview.md) afin de gérer le trafic réseau externe (TCP). Avec une configuration supplémentaire, l’équilibrage de charge et le routage du trafic HTTP ou HTTPS ou des scénarios plus avancés sont possibles.

## <a name="prerequisites"></a>Conditions préalables
* [Déployer un cluster Kubernetes](container-service-kubernetes-walkthrough.md) dans Azure Container Service
* [Connecter votre client](../container-service-connect.md) à votre cluster

## <a name="azure-load-balancer"></a>Équilibrage de charge Azure

Par défaut, un cluster Kubernetes déployé dans le Azure Container Service inclut un équilibrage de charge Azure accessible sur Internet pour les machines virtuelles de l’agent. (Une ressource d’équilibrage de charge distincte est configurée pour les machines virtuelles principales). L’équilibrage de charge Azure est de type Couche 4. Actuellement, l’équilibrage de charge ne prend en charge que le trafic TCP Kubernetes.

Lorsque vous créez un service Kubernetes, vous pouvez configurer automatiquement l’équilibrage de charge Azure afin d’autoriser l’accès au service. Pour configurer l’équilibrage de charge, définissez le service `type` sur `LoadBalancer`. L'équilibrage de charge crée une règle qui mappe l'adresse IP publique et le numéro de port du trafic du service entrant aux adresses IP privées et aux numéros de port des blocs dans les machines virtuelles de l’agent (et inversement pour le trafic de réponse. 

 Voici deux exemples montrant comment définir le service Kubernetes `type` sur `LoadBalancer`. (Après avoir testé ces exemples, supprimez les déploiements si vous n’en avez plus besoin).

### <a name="example-use-the-kubectl-expose-command"></a>Par exemple : utilisez la commande `kubectl expose` 
La [procédure pas à pas Kubernetes](container-service-kubernetes-walkthrough.md) inclut un exemple montrant comment exposer un service avec la commande `kubectl expose` et son indicateur `--type=LoadBalancer`. Voici la procédure à suivre :

1. Démarrez un nouveau déploiement de conteneur. Par exemple, la commande suivante démarre un nouveau déploiement appelé `mynginx`. Le déploiement se compose de trois conteneurs en fonction de l’image Docker pour le serveur web Nginx.

    ```console
    kubectl run mynginx --replicas=3 --image nginx
    ```
2. Vérifiez que les contrôleurs sont en cours d’exécution. Par exemple, si vous interrogez les conteneurs avec `kubectl get pods`, vous voyez une sortie similaire à ce qui suit :

    ![Obtenir des conteneurs Nginx](./media/container-service-kubernetes-load-balancing/nginx-get-pods.png)

3. Pour configurer l’équilibrage de charge afin d’accepter le trafic externe au déploiement, exécutez `kubectl expose` avec `--type=LoadBalancer`. La commande suivante expose le serveur Nginx sur le port 80 :

    ```console
    kubectl expose deployments mynginx --port=80 --type=LoadBalancer
    ```

4. Tapez `kubectl get svc` pour afficher l’état des services dans le cluster. Pendant que l’équilibrage de charge configure la règle, l’élément `EXTERNAL-IP` du service apparaît en tant que `<pending>`. Après quelques minutes, l’adresse IP externe est configurée : 

    ![Configurer l’équilibrage de charge Azure](./media/container-service-kubernetes-load-balancing/nginx-external-ip.png)

5. Vérifiez que vous pouvez accéder au service avec l’adresse IP externe. Par exemple, ouvrez un navigateur web à l’adresse IP indiquée. Le navigateur affiche le serveur web Nginx en cours d’exécution dans un des conteneurs. Ou exécutez la commande `curl` ou `wget`. Par exemple :

    ```
    curl 13.82.93.130
    ```

    La sortie doit ressembler à celle-ci :

    ![Accéder à Nginx avec Curl](./media/container-service-kubernetes-load-balancing/curl-output.png)

6. Pour afficher la configuration de l’équilibrage de charge Azure, accédez au [portail Azure](https://portal.azure.com).

7. Recherchez le groupe de ressources du cluster de votre service de conteneur et sélectionnez l’équilibrage de charge pour les machines virtuelles de l’agent. Son nom devrait être identique à celui du service de conteneur. (Ne choisissez pas l’équilibrage de charge pour les nœuds principaux, celui dont le nom inclut **master kg**.) 

    ![Équilibrage de charge dans le groupe de ressources](./media/container-service-kubernetes-load-balancing/container-resource-group-portal.png)

8. Pour afficher les détails de la configuration d’équilibrage de charge, cliquez sur **Règles d’équilibrage de la charge** et sur le nom de la règle qui a été configurée.

    ![Règles d'équilibrage de charge](./media/container-service-kubernetes-load-balancing/load-balancing-rules.png) 

### <a name="example-specify-type-loadbalancer-in-the-service-configuration-file"></a>Exemple : spécifiez `type: LoadBalancer` dans le fichier de configuration de service

Si vous déployez une application de conteneur Kubernetes à partir d’un [fichier de configuration de service](https://kubernetes.io/docs/user-guide/services/operations/#service-configuration-file) YAML ou JSON, spécifiez un équilibreur de charge externe en ajoutant la ligne suivante à la spécification du service :

```YAML
 "type": "LoadBalancer"
``` 



Les étapes suivantes utilisent l’[exemple Guestbook (livre d’or)](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook) Kubernetes. Cet exemple s’appuie sur une application web multiniveau basée sur des images Redis et PHP Docker. Dans le fichier de configuration de service, vous pouvez spécifier que le serveur PHP frontal utilise l’équilibrage de charge Azure.

1. Téléchargez le fichier `guestbook-all-in-one.yaml` à partir de [GitHub](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook/all-in-one). 
2. Recherchez l’élément `spec` pour le service `frontend`.
3. Supprimez les marques de commentaire de la ligne `type: LoadBalancer`.

    ![Équilibrage de charge dans la configuration du service](./media/container-service-kubernetes-load-balancing/guestbook-frontend-loadbalance.png)

4. Enregistrez le fichier, puis déployez l’app en exécutant la commande ci-dessous :

    ```
    kubectl create -f guestbook-all-in-one.yaml
    ```

5. Tapez `kubectl get svc` pour afficher l’état des services dans le cluster. Pendant que l’équilibrage de charge configure la règle, l’élément `EXTERNAL-IP` du service `frontend` apparaît sous la forme `<pending>`. Après quelques minutes, l’adresse IP externe est configurée : 

    ![Configurer l’équilibrage de charge Azure](./media/container-service-kubernetes-load-balancing/guestbook-external-ip.png)

6. Vérifiez que vous pouvez accéder au service avec l’adresse IP externe. Par exemple, vous pouvez ouvrir un navigateur web à l’adresse IP externe du service.

    ![Accéder en externe au livre d’or](./media/container-service-kubernetes-load-balancing/guestbook-web.png)

    Vous pouvez ajouter des entrées du livre d’or.

7. Pour afficher la configuration de l’équilibrage de charge Azure, recherchez la ressource d’équilibrage de charge du cluster dans le [portail Azure](https://portal.azure.com). Consultez les étapes décrites dans l’exemple précédent.

### <a name="considerations"></a>Considérations

* La création de la règle d’équilibrage de charge est effectuée de façon asynchrone, et des informations concernant l’équilibrage approvisionné sont publiées dans le champ `status.loadBalancer` du service.
* Une adresse IP virtuelle est automatiquement attribuée à chaque service dans l’équilibreur de charge.
* Si vous souhaitez accéder à l’équilibrage de charge à l’aide d’un nom DNS, contactez votre fournisseur de services de domaine afin de créer un nom DNS pour l’adresse IP de la règle.

## <a name="http-or-https-traffic"></a>Trafic HTTP ou HTTPS

Pour équilibrer le trafic HTTP ou HTTPS des applications web de conteneur et gérer les certificats du protocole TLS (Transport Security Layer), vous pouvez utiliser la ressource [Entrée](https://kubernetes.io/docs/user-guide/ingress/) de Kubernetes. Une entrée est une collection de règles qui autorisent les connexions entrantes à joindre les services du cluster. Pour qu’une ressource Entrée fonctionne, le cluster Kubernetes doit avoir un [contrôleur d’entrée](https://kubernetes.io/docs/user-guide/ingress/#ingress-controllers) en cours d’exécution.

Azure Container Service n’implémente pas automatiquement un contrôleur d’entrée Kubernetes. Plusieurs implémentations de contrôleur sont disponibles. Actuellement, le [contrôleur d’entrée Nginx](https://github.com/kubernetes/ingress/tree/master/examples/deployment/nginx) est recommandé pour configurer les règles d’entrée et équilibrer la charge du trafic HTTP et HTTPS. 

Pour plus d’informations, consultez la [documentation du contrôleur d’entrée Nginx](https://github.com/kubernetes/ingress/tree/master/controllers/nginx/README.md).

> [!IMPORTANT]
> Lorsque vous utilisez le contrôleur d’entrée Nginx dans Azure Container Service, vous devez exposer le déploiement du contrôleur en tant que service avec `type: LoadBalancer`. Cette opération configure l’équilibrage de charge Azure pour acheminer le trafic vers le contrôleur. Pour plus d'informations, consultez la section précédente.


## <a name="next-steps"></a>Étapes suivantes

* Consultez la [documentation de l’équilibrage de charge Kubernetes](https://kubernetes.io/docs/user-guide/load-balancer/)
* En savoir plus sur les [contrôleurs d’entrée, en particulier le contrôleur d’entrée Kubernetes](https://kubernetes.io/docs/user-guide/ingress/)
* Consultez les [exemples Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples)


