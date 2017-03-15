---
title: "Registres de conteneurs Docker privés dans Azure | Microsoft Docs"
description: "Présentation du service Azure Container Registry, proposant des registres Docker privés, gérés et basés sur le cloud."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0433e22dc7722ef9c8edfaf949dbd9a9d8645e67
ms.openlocfilehash: 69d1750f13b5507268229b29a392c38662c0f5f0
ms.lasthandoff: 03/02/2017

---
# <a name="introduction-to-private-docker-container-registries"></a>Présentation des registres de conteneurs Docker privés
> [!NOTE]
> Container Registry est actuellement en version préliminaire.


Azure Container Registry est un service de [registre Docker](https://docs.docker.com/registry/) géré basé sur le Registre open source Docker v2. Créez et gérez des registres de conteneur Azure pour stocker et gérer vos images de [conteneur Docker](https://www.docker.com/what-docker) privées. Utilisez des registres de conteneur dans Azure avec vos pipelines de développement et de déploiement existants, et exploitez l’expertise de la communauté de Docker.

Pour plus d’informations sur Docker et les conteneurs, voir :

* [Guide d'utilisation Docker](https://docs.docker.com/engine/userguide/)
* [Annonce de la version préliminaire d’Azure Container Registry](https://azure.microsoft.com/blog/azure-container-registry-preview/) 

## <a name="key-concepts"></a>Concepts clés
* **Registre** : créez au moins un registre de conteneur dans votre abonnement Azure. Chaque registre est pris en charge par un [compte de stockage](../storage/storage-introduction.md) Azure standard dans le même emplacement. Créez un registre dans le même emplacement Azure que vos déploiements afin de tirer parti du stockage local proche du réseau de vos images de conteneur. 

  Les registres sont nommés dans un domaine racine basé sur le [client Azure Active Directory](../active-directory/active-directory-howto-tenant.md) de l’abonnement. Par exemple, si vous avez un compte professionnel dans le domaine Contoso, votre nom complet de registre se présente sous la forme `myregistry-contoso.azurecr.io`. 
  
  Vous [contrôlez l’accès](container-registry-authentication.md) à un registre de conteneur à l’aide d’un [principal de service](../active-directory/active-directory-application-objects.md) pris en charge par Azure Active Directory ou un compte d’administration fourni. Exécutez la commande `docker login` standard pour vous authentifier auprès d’un registre. 

* **Référentiel** : un registre contient un ou plusieurs référentiels, qui sont des groupes d’images de conteneur. Azure Container Registry prend en charge les espaces de noms de référentiel à plusieurs niveaux. Cette fonctionnalité vous permet de regrouper des collections d’images liées à une application spécifique, ou une collection d’applications à des équipes opérationnelles ou de développement spécifiques. Par exemple :
  
  * `myregistry-contoso.azurecr.io/aspnetcore:1.0.1` représente une image de l’entreprise.
  * `myregistry-contoso.azurecr.io/warrantydept/dotnet-build` représente une image utilisée pour créer des applications .NET, partagées par le service de garantie.
  * `myregistry-contoso.azrecr.io/warrantydept/customersubmissions/web` représente une image web, regroupée dans l’application constomersubmissions, détenue par le service de garantie.

* **Image** : stockée dans un référentiel, chaque image est une capture instantanée en lecture seule d’un conteneur Docker. Les registres de conteneur Azure peuvent inclure des images de Windows et Linux. Vous contrôlez les noms d’images pour tous les déploiements de votre conteneur. Utilisez des [commandes Docker](https://docs.docker.com/engine/reference/commandline/) standard pour envoyer les images dans un référentiel ou extraire une image d’un référentiel. 

* **Conteneur** : un conteneur définit une application logicielle et ses dépendances encapsulées dans un système de fichiers complet, y compris le code, l’exécution, les outils système et les bibliothèques. Exécutez des conteneurs Docker basés sur des images Windows ou Linux que vous extrayez à partir d’un registre de conteneur. Les conteneurs en cours d’exécution sur un même ordinateur partagent le noyau du système d’exploitation. Les conteneurs Docker sont entièrement portables sur toutes les principales distributions Linux, Mac et Windows.

## <a name="use-cases"></a>Cas d'utilisation
Extrayez des images à partir d’un registre de conteneur Azure pour différents objectifs de déploiement :

* Des **systèmes d’orchestration évolutifs** qui gèrent des applications en conteneur sur des clusters d’hôtes, y compris du [contrôleur de domaine/système d’exploitation](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) et [Kubernetes](http://kubernetes.io/docs/).
* Des **services Azure** qui prennent en charge la création et l’exécution des applications à grande échelle, y compris [Container Service](../container-service/index.md), [App Service](/app-service/index.md), [Batch](../batch/index.md) et [Service Fabric](../service-fabric/index.md). 

Les développeurs peuvent également effectuer un push vers un registre de conteneur dans le cadre d’un flux de travail de développement de conteneur. Par exemple, ciblez un registre de conteneur à partir d’un outil de développement et d’intégration continu comme [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) ou [Jenkins](https://jenkins.io/).





## <a name="next-steps"></a>Étapes suivantes
* [Créer un registre de conteneur à l’aide du portail Azure](container-registry-get-started-portal.md)
* [Créer un registre de conteneur à l’aide de l’interface de ligne de commande Azure](container-registry-get-started-azure-cli.md)
* [Effectuer un push de votre première image à l’aide de l’interface CLI Docker](container-registry-get-started-docker-cli.md)
* Pour générer une intégration continue et un workflow de déploiement à l’aide de Visual Studio Team Services, Azure Container Service et Azure Container Registry, consultez [ce didacticiel](../container-service/container-service-setup-ci-cd.md).
* Si vous souhaitez configurer votre propre registre privé Docker dans Azure (sans les points de terminaison publics), consultez [Déploiement de votre propre registre Docker privé sur Azure](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md).

