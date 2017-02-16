---
title: "Connexion à un cluster Azure Container Service | Microsoft Docs"
description: "Connexion à un cluster Kubernetes, DC/OS ou Docker Swarm dans Azure Container Service à partir d’un ordinateur distant"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Kubernetes, DC/OS, Azure
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: ea59ff3f527d051e01baf12f596ff44af8a0dfc1
ms.openlocfilehash: 7fe3bc6a5eab1d1b9a8b73ab3c88f9808817369a


---
# <a name="connect-to-an-azure-container-service-cluster"></a>Connexion à un cluster Azure Container Service
Après avoir créé un cluster Azure Container Service, vous devez vous connecter au cluster pour déployer et gérer des charges de travail. Cet article décrit comment se connecter à la machine virtuelle principale du cluster à partir d’un ordinateur distant. Tous les clusters Kubernetes,DC/OS et Docker Swarm exposent des points de terminaison REST. Pour Kubernetes, ce point de terminaison est exposé en toute sécurité sur Internet et vous pouvez y accéder en exécutant l’outil en ligne de commande `kubectl` depuis n’importe quel ordinateur connecté à Internet. Pour DC/OS et Docker Swarm, vous devez créer un tunnel Secure Shell (SSH) pour garantir une connexion sécurisée au point de terminaison REST. 

> [!NOTE]
> La prise en charge de Kubernetes dans l’Azure Container Service est actuellement en version préliminaire.
>

## <a name="prerequisites"></a>Composants requis

* Un cluster Kubernetes, DC/OS ou Swarm [déployé dans Azure Container Service](container-service-deployment.md).
* Fichier de clé privée SSH, correspondant à la clé publique ajoutée au cluster pendant le déploiement. Ces commandes supposent que la clé SSH privée se trouve dans `$HOME/.ssh/id_rsa` sur votre ordinateur. Pour plus d’informations, consultez ces instructions pour [OS X et Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). Si la connexion SSH ne fonctionne pas, vous devrez peut-être [réinitialiser vos clés SSH](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Connexion à un cluster Kubernetes

Suivez ces étapes pour installer et configurer `kubectl` sur votre ordinateur.

> [!NOTE] 
> Sur Linux ou OS X, vous devrez peut-être exécuter les commandes de cette section à l’aide de `sudo`.
> 

### <a name="install-kubectl"></a>Installer kubectl
Pour installer cet outil, utilisez la commande de l’interface Azure CLI 2.0 (version préliminaire) `az acs kubernetes install cli`. Pour exécuter cette commande, assurez-vous que vous avez [installé](/cli/azure/install-az-cli2) la dernière version de l’interface Azure CLI 2.0 (version préliminaire) et que vous vous êtes connecté à un compte Azure (`az login`).

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Vous pouvez également télécharger le client directement à partir de la [page des publications](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md#downloads-for-v146).

### <a name="download-cluster-credentials"></a>Télécharger des informations d’identification du cluster
Une fois que `kubectl` est installé, vous devez copier les informations d’identification du cluster sur votre ordinateur. Vous pouvez obtenir les informations d’identification à l’aide de la commande `az acs kubernetes get-credentials`. Transmettez le nom du groupe de ressources et le nom de la ressource du service de conteneur :


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Cette commande télécharge les informations d’identification du cluster dans `$HOME/.kube/config` où `kubectl` devrait se trouver.

Vous pouvez également utiliser `scp` pour copier en toute sécurité le fichier depuis `$HOME/.kube/config` sur la machine virtuelle maître vers votre ordinateur local. Par exemple :

```console
mkdir $HOME/.kube/config
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Si vous êtes sous Windows, vous devez utiliser Bash sur Ubuntu sur Windows, le client de copie de fichiers sécurisée PuTTy ou un outil similaire.



### <a name="use-kubectl"></a>Utiliser kubectl

Une fois que vous avez configuré `kubectl`, vous pouvez tester la connexion en répertoriant les nœuds de votre cluster :

```console
kubectl get nodes
```

Vous pouvez essayer d’autres commandes `kubectl`. Par exemple, vous pouvez voir le tableau de bord Kubernetes. Exécutez tout d’abord un proxy vers le serveur d’API Kubernetes :

```console
kubectl proxy
```

L’interface utilisateur Kubernetes est désormais disponible à l’adresse : `http://localhost:8001/ui`.

Pour plus d’informations, consultez l’article sur le [démarrage rapide de Kubernetes](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Connexion à un cluster DC/OS ou Swarm

Les clusters DC/OS et Docker Swarm déployés par Azure Container Service exposent des points de terminaison REST. Toutefois, ces points de terminaison ne sont pas ouverts au monde extérieur. Pour gérer ces points de terminaison, vous devez créer un tunnel Secure Shell (SSH). Une fois le tunnel SSH établi, vous pouvez exécuter des commandes sur les points de terminaison du cluster et afficher l’interface utilisateur du cluster par le biais d’un simple navigateur exécuté sur votre propre système. Les sections suivantes vous guident tout au long de la création d’un tunnel SSH à partir d’ordinateurs exécutant des systèmes d’exploitation Linux, OS X et Windows.

> [!NOTE]
> Vous pouvez créer une session SSH avec un système de gestion de cluster. Toutefois, nous ne recommandons pas cela. Le travail direct avec un système de gestion, vous risquez de modifier la configuration par inadvertance.
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Créer un tunnel SSH sur Linux ou OS X
La première chose que vous faites lorsque vous créez un tunnel SSH sur Linux ou OS X est de rechercher le nom DNS public des maîtres de l’équilibrage de masque. Procédez comme suit :


1. Dans le [portail Azure](https://portal.azure.com), accédez au groupe de ressources qui contient votre cluster de service de conteneur. Développez le groupe de ressources de manière à afficher chaque ressource. 

2. Recherchez et sélectionnez la machine virtuelle du serveur principal. Dans un cluster DC/OS, le nom de cette ressource commence par **dcos-master -**. 

    Le panneau **Machine virtuelle** contient des informations sur l’adresse IP publique, et notamment le nom DNS. Enregistrez ce nom pour pouvoir l’utiliser ultérieurement. 

    ![Nom DNS public](media/pubdns.png)

3. Ouvrez un interpréteur de commandes et exécutez la commande `ssh` en spécifiant les valeurs suivantes : 

    **PORT** désigne le port du point de terminaison que vous souhaitez exposer. Pour Swarm, utilisez le port 2375. Pour DC/OS, utilisez le port 80.  
    **USERNAME** est le nom d’utilisateur fourni lors du déploiement du cluster.  
    **DNSPREFIX** est le préfixe DNS que vous avez fourni lors du déploiement du cluster.  
    **REGION** est la région dans laquelle se trouve le groupe de ressources.  
    **PATH_TO_PRIVATE_KEY** [FACULTATIF] est le chemin d’accès à la clé privée correspondant à la clé publique que vous avez fournie lorsque vous avez créé le cluster. Utilisez cette option avec l’indicateur `-i`.

    ```bash
    ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
    ```
    > [!NOTE]
    > Le port de connexion SSH est 2200, et non le port 22 standard. Dans un cluster avec plus d’une machine virtuelle maître, il s’agit du port de connexion vers la première machine virtuelle maître.
    > 

Consultez les exemples pour DC/OS et Swarm dans les sections suivantes.    

### <a name="dcos-tunnel"></a>Tunnel DC/OS
Pour ouvrir un tunnel vers les points de terminaison liés au cluster DC/OS, exécutez une commande semblable à l’exemple suivant :

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez accéder maintenant les points de terminaison associés à DC/OS :

* DC/OS : `http://localhost/`
* Marathon : `http://localhost/marathon`
* Mesos : `http://localhost/mesos`

De la même façon, les API REST correspondant à chaque application sont accessibles par ce tunnel.

### <a name="swarm-tunnel"></a>Tunnel Swarm
Pour ouvrir un tunnel vers un point de terminaison lié au cluster Swarm, exécutez une commande semblable à l’exemple suivant :

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Vous pouvez à présent définir votre variable d’environnement DOCKER_HOST comme suit. Vous pouvez continuer à utiliser votre interface de ligne de commande (CLI) Docker comme d’habitude.

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Créer un tunnel SSH sur Windows
Il existe plusieurs options de création des tunnels SSH sous Windows. Cette section décrit comment utiliser PuTTY pour créer le tunnel.

1. [Téléchargez PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) sur votre système Windows.

2. Exécutez l'application.

3. Saisissez un nom d’hôte constitué du nom d’utilisateur admin des clusters et du nom DNS public du premier serveur principal du cluster. Le **nom d’hôte** est similaire à `adminuser@PublicDNSName`. Spécifiez le **port**2200.

    ![Configuration PuTTY 1](media/putty1.png)

4. Sélectionnez **SSH > Auth**. Ajoutez un chemin d’accès à votre fichier de clé privée (format .ppk) pour l’authentification. Vous pouvez utiliser un outil tel que [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) pour générer ce fichier à partir de la clé SSH utilisée pour créer le cluster.

    ![Configuration PuTTY 2](media/putty2.png)

5. Sélectionnez **SSH > Tunnels** et configurez les ports transférés suivants :

    * **Port source :** utilisez 80 pour DC/OS et 2375 pour Swarm.
    * **Destination :** utilisez localhost:80 pour DC/OS ou localhost:2375 pour Swarm.

    L’exemple suivant est configuré pour DC/OS, mais Docker Swarm pourrait présenter un aspect similaire.

    > [!NOTE]
    > Le port 80 ne doit pas être en cours d’utilisation lors de la création de ce tunnel.
    > 

    ![Configuration PuTTY 3](media/putty3.png)

6. Quand vous avez terminé, cliquez sur **Session > Enregistrer** pour enregistrer la configuration de la connexion.

7. Pour vous connecter à la session PuTTY, cliquez sur **Ouvrir**. Une fois connecté, vous pouvez configurer la configuration du port dans le journal d’événements PuTTY.

    ![Journal des événements PuTTY](media/putty4.png)

Lorsque vous avez configuré le tunnel de DC/OS, vous pouvez accéder au point de terminaison associé aux adresses suivantes :

* DC/OS : `http://localhost/`
* Marathon : `http://localhost/marathon`
* Mesos : `http://localhost/mesos`

Une fois que vous avez configuré le tunnel pour Docker Swarm, ouvrez les paramètres Windows pour configurer une variable d’environnement système nommée `DOCKER_HOST` avec la valeur `:2375`. Puis, vous pouvez accéder au cluster Swarm via l’interface de ligne de commande Docker.

## <a name="next-steps"></a>Étapes suivantes
Déployer et gérer des conteneurs dans votre cluster :

* [Gestion des conteneurs avec Kubernetes](container-service-kubernetes-ui.md)
* [Gestion de conteneur via l’API REST](container-service-mesos-marathon-rest.md)
* [Gestion des conteneurs avec Docker Swarm](container-service-docker-swarm.md)




<!--HONumber=Jan17_HO3-->


