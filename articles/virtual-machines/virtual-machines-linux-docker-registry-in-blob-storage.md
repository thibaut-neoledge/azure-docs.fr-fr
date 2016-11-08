---
title: Déploiement de votre propre registre Docker privé sur Azure | Microsoft Docs
description: Décrit comment vous pouvez utiliser Docker Registry pour héberger vos images de conteneur sur le service Azure Blob Storage.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ahmetalpbalkan
editor: squillace
manager: timlt
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/27/2016
ms.author: ahmetb

---
# <a name="deploying-your-own-private-docker-registry-on-azure"></a>Déploiement de votre propre registre Docker privé sur Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Ce document décrit ce qu’est un registre privé Docker et montre comment vous pouvez déployer une image de conteneur Docker Registry 2.0 vers un registre privé Docker sur Microsoft Azure en utilisant Azure Blob Storage.

Ce document suppose que :

1. Vous savez comment utiliser Docker et vous avez des images Docker à stocker. (Ce n’est pas le cas ? [Découvrez Docker](https://www.docker.com))
2. Vous disposez d’un serveur où le moteur Docker est installé. (Ce n’est pas le cas ? [Faites-le rapidement sur Azure](https://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/).)

## <a name="what-is-a-private-docker-registry?"></a>Qu’est-ce qu’un registre Docker privé ?
Pour envoyer vos applications en conteneur dans le cloud, vous construisez une image de conteneur Docker et vous le stockez à un endroit où il peut être utilisé par vous-même et par d’autres. 

Si créer une image de conteneur et l’envoyer dans le cloud est une opération facile, il est cependant plus difficile de stocker l’image générée de manière fiable. Pour cette raison, Docker offre un service centralisé appelé [Docker Hub][docker-hub] pour stocker les images de conteneur dans le cloud et qui vous permet de créer des conteneurs à tout moment en utilisant ces images.

Bien que [Docker Hub][docker-hub] soit un service payant pour le stockage de vos images de conteneur d’application privées, Docker répond aux besoins des développeurs et fournit un ensemble d’outils open source pour stocker vos images dans votre propre registre Docker privé derrière un pare-feu ou localement, sans s’appuyer sur l’internet public.
Azure Blob Storage étant facile à sécuriser, vous pouvez rapidement l’utiliser pour créer et utiliser un registre Docker privé dans Azure que vous contrôlez vous-même.

## <a name="why-should-you-host-a-docker-registry-on-azure?"></a>Pourquoi héberger un registre Docker sur Azure ?
En hébergeant votre instance Docker Registry sur Microsoft Azure et en stockant vos images sur Azure Blob Storage, vous pouvez bénéficier de plusieurs avantages :

**Sécurité :** vos images Docker ne quittent pas les centres de données Azure et elles ne circulent donc pas sur l’internet public, comme ce serait le cas si vous utilisiez Docker Hub.

**Performances :** vos images Docker sont stockées dans le même centre de données ou la même région que vos applications. Cela signifie que les images seront extraites plus rapidement et de façon plus fiable par rapport à Docker Hub.

**Fiabilité :** en utilisant Microsoft Azure Blob Storage, vous pouvez utiliser de nombreuses propriétés du stockage, comme la haute disponibilité, la redondance, le stockage premium (SSD), etc.

## <a name="configuring-docker-registry-to-use-azure-blob-storage"></a>Configuration de Docker Registry pour l’utilisation d’Azure Blob Storage
(Il est recommandé de lire la [documentation de Docker Registry 2.0][registry-docs] avant de continuer.)

Vous pouvez [configurer][registry-config] votre registre Docker de deux manières différentes.
Vous pouvez :

1. Utiliser un fichier `config.yml` . Dans ce cas, vous devez créer une image Docker distincte par-dessus l’image `registry`.
2. Remplacer le fichier de configuration par défaut via des variables d’environnement : tout se fait donc sans créer ni gérer une image Docker distincte.

Par souci de simplicité, cette rubrique suit l’option 2, en utilisant les variables d’environnement.

Pour exécuter une instance de Docker Registry qui :

* utilise votre compte Azure Storage pour stocker les images
* écoute sur le port 5000 de la machine virtuelle
* n’a pas de méthode d’authentification configurée (non recommandé, voir la remarque ci-dessous)

vous devez exécuter la commande Docker suivante dans votre terminal Bash (remplacez `<storage-account>` et `<storage-key>` par vos informations d’identification) :

```sh
$ docker run -d -p 5000:5000 \
     -e REGISTRY_STORAGE=azure \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTNAME="<storage-account>" \
     -e REGISTRY_STORAGE_AZURE_ACCOUNTKEY="<storage-key>" \
     -e REGISTRY_STORAGE_AZURE_CONTAINER="registry" \
     --name=registry \
     registry:2
```

Une fois la commande terminée, vous pouvez voir le conteneur qui héberge votre instance Docker Registry privée en exécutant la commande `docker ps` sur votre hôte :

```sh
$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                    NAMES
3698ddfebc6f        registry:2          "registry cmd/regist   2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp   registry
```

> [!IMPORTANT]
> La configuration de la sécurité pour le registre Docker n’est pas abordée dans ce document. Votre registre sera accessible par défaut à toute personne sans authentification si vous ouvrez le port pour le port du registre sur le point de terminaison ou l’équilibreur de charge de la machine virtuelle si vous utilisez la commande de déploiement ci-dessus.
> 
> Lisez la documentation [Configuration de Docker Registry][registry-config] pour savoir comment sécuriser l’instance du registre et vos images.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Une fois le registre configuré, vous pouvez l’utiliser. Démarrez avec le docker [registry-docs]. 

[docker-hub]: https://hub.docker.com/
[registry]: https://github.com/docker/distribution
[registry-docs]: http://docs.docker.com/registry/
[registry-config]: http://docs.docker.com/registry/configuration/




<!--HONumber=Oct16_HO2-->


