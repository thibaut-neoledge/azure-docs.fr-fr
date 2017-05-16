---
title: "Utilisation d’un ACR avec un cluster DC/OS Azure | Microsoft Docs"
description: Utiliser un Azure Container Registry avec un cluster DC/OS dans Azure Container Service
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: Docker, conteneurs, micro-services, Mesos, Azure, FileShare, cifs
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a8a3716f8d03b596285026426c7514b7b642cb25
ms.lasthandoff: 03/31/2017


---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Utiliser un ACR avec un cluster DC/OS pour déployer votre application

Dans cet article, nous allons découvrir comment utiliser un registre de conteneur privé comme ACR (Azure Container Registry) avec un cluster DC/OS. Grâce à ACR, vous pouvez stocker en privé des images et conserver le contrôle de celles-ci, par exemple leurs versions et/ou leurs mises à jour.

Avant d’étudier cet exemple, vous devez avoir : 
* Un cluster DC/OS configuré dans Azure Container Service. Consultez [Déploiement d’un cluster Azure Container Service](container-service-deployment.md).
* Un Azure Container Service déployé. Consultez la page [Créer un registre de conteneur Docker privé à l’aide du portail Azure](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal) ou [Créer un registre de conteneur Docker privé à l’aide d’Azure CLI 2.0](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli)
* Un partage de fichiers configuré à l’intérieur de votre cluster DC/OS. Consultez la page [Créer et monter un partage de fichiers sur un cluster DC/OS](container-service-dcos-fileshare.md)
* Comprendre le déploiement d’une image Docker dans un cluster DC/OS à l’aide de [l’interface utilisateur Web](container-service-mesos-marathon-ui.md) ou de [l’API REST](container-service-mesos-marathon-rest.md)

## <a name="manage-the-authentication-inside-your-cluster"></a>Gérer l’authentification à l’intérieur de votre cluster

La méthode traditionnelle pour ajouter et extraire une image d’un registre privé consiste tout d’abord à l’authentifier auprès de celui-ci. Pour ce faire, vous devez utiliser la ligne de commande `docker login` sur tout processus de client Docker qui aura besoin de votre registre privé.
Dans un environnement de production, en utilisant DC/OS dans notre cas, vous devez vous assurer que vous êtes en mesure d’extraire des images à partir de n’importe quel nœud. Cela signifie que vous devez automatiser le processus d’authentification et ne pas exécuter la ligne de commande sur chaque machine, car comme vous pouvez l’imaginer, selon la taille de votre cluster, cette opération peut se révéler longue et difficile. 

En supposant que vous ayez déjà [configuré un partage de fichiers dans votre DC/OS](container-service-dcos-fileshare.md), nous allons en tirer parti en procédant ainsi :

### <a name="from-any-client-machine-recommended-method"></a>À partir de n’importe quelle machine cliente [méthode recommandée]

Les commandes suivantes sont exécutables sur n’importe quel environnement (Windows/Mac/Linux) :

1. Assurez-vous que les conditions préalables suivantes sont réunies :
  * Outil TAR
    * [Windows](http://gnuwin32.sourceforge.net/packages/gtar.htm)
  * Docker 
    * [Windows](https://www.docker.com/docker-windows)
    * [MAC](https://www.docker.com/docker-mac)
    * [Ubuntu](https://www.docker.com/docker-ubuntu)
    * [Autres](https://www.docker.com/get-docker)
  * Partage de fichiers monté à l’intérieur de votre cluster, [avec la méthode suivante](container-service-dcos-fileshare.md)

2. Lancez l’authentification auprès de votre service ACR à l’aide de la commande suivante dans votre terminal préféré : `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. Vous devez remplacer les variables `USERNAME`, `PASSWORD` et `ACR-REGISTRY-NAME` par les valeurs fournies sur votre portail Azure

3. Il est intéressant de savoir que lorsque vous effectuez une opération `docker login`, les valeurs sont stockées localement sur l’ordinateur dans votre dossier de base (`cd ~/.docker` sur Mac et Linux ou `cd %HOMEPATH%` sur Windows). Nous comprimerons le contenu de ce dossier à l’aide de la commande `tar czf`.

4. L’étape finale consiste à copier le fichier tar que nous venons de créer à l’intérieur du partage de fichiers [que vous aurez créé en tant que condition préalable](container-service-dcos-fileshare.md). Vous pouvez le faire avec la commande suivante dans l’interface CLI Azure`az storage file upload -s <shareName> --account-name <storageAccountName> --account-key <storageAccountKey> -source <pathToTheTarFile>`

Pour conclure, voici un exemple avec la configuration suivante (dans un environnement Windows) :
* Nom ACR : **`demodcos`**
* Nom d’utilisateur : **`demodcos`**
* Mot de passe :**`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Nom du compte de stockage : **`anystorageaccountname`**
* Clé du compte de stockage : **`aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg==`**
* Nom du partage créé à l’intérieur du compte de stockage :**`share`**
* Chemin d’accès à l’archive tar à télécharger :**`%HOMEPATH%/.docker/docker.tar.gz`**

```bash
# Changing directory to the home folder of the default user
cd %HOMEPATH%

# Authentication into my ACR
docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
tar czf docker.tar.gz .docker

# Upload the tar archive in the fileshare
az storage file upload -s share --account-name anystorageaccountname --account-key aYGl6Nys4De5J3VPldT1rXxz2+VjgO7dgWytnoWClurZ/l8iO5c5N8xXNS6mpJhSc9xh+7zkT7Mr+xIT4OIVMg== --source %HOMEPATH%/docker.tar.gz
```

### <a name="from-the-master-not-recommended-method"></a>À partir du master [méthode déconseillée]

L’exécution de l’opération à partir du master n’est pas recommandée pour éviter les erreurs et l’impact sur les environnements entiers.

1. Tout d’abord, connectez-vous par SSH au master (ou premier master) de votre cluster basé sur DC/OS. Par exemple, `ssh userName@masterFQDN –A –p 22`, où masterFQDN est le nom de domaine complet de la machine virtuelle principale. [Pour plus d’informations, cliquez ici](https://docs.microsoft.com/azure/container-service/container-service-connect#connect-to-a-dcos-or-swarm-cluster)

2. Lancez l’authentification auprès de votre service ACR à l’aide de la commande suivante : `sudo docker login --username=<USERNAME> --password=<PASSWORD> <ACR-REGISTRY-NAME>.azurecr.io`. Vous devez remplacer les variables `USERNAME`, `PASSWORD` et `ACR-REGISTRY-NAME` par les valeurs fournies sur votre portail Azure

3. Il est intéressant de savoir que lorsque vous effectuez une opération `docker login`, les valeurs sont stockées localement sur l’ordinateur dans votre dossier de base `~/.docker`. Nous comprimerons le contenu de ce dossier à l’aide de la commande `tar czf`.

4. L’étape finale consiste à copier le fichier tar que nous venons de créer à l’intérieur du partage de fichiers. Cette opération permettra, sur toutes les machines virtuelles au sein de notre cluster, d’utiliser ces informations d’identification et de les authentifier auprès d’Azure Container Registry.

Pour conclure, voici un exemple avec la configuration suivante :
* Nom ACR : **`demodcos`**
* Nom d’utilisateur : **`demodcos`**
* Mot de passe :**`+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0`**
* Point de montage à l’intérieur du cluster :**`/mnt/share`**

```bash
# Changing directory to the home folder of the default user
cd ~

# Authentication into my ACR
sudo docker login --username=demodcos --password=+js+/=I1=L+D=+eRpU+/=wI/AjvDo=J0 demodcos.azurecr.io

# Tar the contains of the .docker folder
sudo tar czf docker.tar.gz .docker

# Copy of the tar file in the file share of my cluster
sudo cp docker.tar.gz /mnt/share
```


## <a name="deploy-an-image-from-acr-with-marathon"></a>Déployer une image à partir d’ACR avec Marathon

Supposons que vous ayez déjà ajouté les images que vous souhaitez déployer à l’intérieur de votre registre de conteneur. Consultez [Transmission de votre première image vers un Registre de conteneur Docker privé à l’aide de l’interface de ligne de commande (CLI) Docker](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)

Supposons que nous voulions déployer l’image **web simple**, avec la balise **2.1**, à partir de notre registre privé hébergé sur Azure (ACR), nous utiliserons la configuration suivante :

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "demodcos.azurecr.io/simple-web:2.1",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  },
  "uris":  [
       "file:///mnt/share/docker.tar.gz"
   ]
}
```

> [!NOTE] 
> Comme vous pouvez le voir, nous utilisons l’option **uris** pour spécifier où sont stockées nos informations d’identification.
>

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [gestion de vos conteneurs DC/OS](container-service-mesos-marathon-ui.md).
* Gestion de conteneur DC/OS au moyen de [l’API REST Marathon](container-service-mesos-marathon-rest.md).
