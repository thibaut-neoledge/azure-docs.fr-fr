---
title: "Registres de conteneurs Docker privés dans Azure"
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
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 664696d2f355609c76477765c2238c6d62253482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Présentation des registres de conteneurs Docker privés dans Azure

Azure Container Registry est un service de [registre Docker](https://docs.docker.com/registry/) géré basé sur le Registre open source Docker 2.0. Créez et gérez des registres de conteneur Azure pour stocker et gérer vos images de [conteneur Docker](https://www.docker.com/what-docker) privées. Utilisez des registres de conteneur dans Azure avec vos pipelines de développement et de déploiement existants, et exploitez l’expertise de la communauté de Docker.

Pour en savoir plus Docker et des conteneurs, consultez le [guide d’utilisation Docker](https://docs.docker.com/engine/userguide/).

## <a name="use-cases"></a>Cas d'utilisation
Extrayez des images à partir d’un registre de conteneur Azure pour différents objectifs de déploiement :

* Des **systèmes d’orchestration évolutifs** qui gèrent des applications en conteneur sur des clusters d’hôtes, y compris du [contrôleur de domaine/système d’exploitation](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) et [Kubernetes](http://kubernetes.io/docs/).
* Des **services Azure** qui prennent en charge la création et l’exécution des applications à grande échelle, y compris [Container Service](../container-service/index.yml), [App Service](/app-service/index.md), [Batch](../batch/index.md), [Service Fabric](/azure/service-fabric/) et d’autres services.

Les développeurs peuvent également effectuer un push vers un registre de conteneur dans le cadre d’un flux de travail de développement de conteneur. Par exemple, ciblez un registre de conteneur à partir d’un outil de développement et d’intégration continu comme [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) ou [Jenkins](https://jenkins.io/).

## <a name="key-concepts"></a>Concepts clés
* **Registre** : créez au moins un registre de conteneur dans votre abonnement Azure. Chaque registre est pris en charge par un [compte de stockage](../storage/common/storage-introduction.md) Azure standard dans le même emplacement. Tirez parti du stockage local proche du réseau de vos images de conteneur en créant un registre dans le même emplacement Azure que vos déploiements. Un nom de Registre complet se présente sous la forme `myregistry.azurecr.io`.

  Vous [contrôlez l’accès](container-registry-authentication.md) à un registre de conteneur à l’aide d’un [principal de service](../active-directory/active-directory-application-objects.md) pris en charge par Azure Active Directory ou un compte d’administration fourni. Exécutez la commande `docker login` standard pour vous authentifier auprès d’un registre.

* **Registre managé** : vous pouvez opter pour la création d’un registre managé, ou sauvegardé par votre compte de stockage lors de la création d’un registre. Les registres managés proposent des fonctionnalités supplémentaires dans trois références SKU : De base, Standard et Premium. Les images de ces références SKU sont stockées dans les comptes de stockage Microsoft Azure managés par le service Azure Container Registry, qui améliore la fiabilité et active de nouvelles fonctions. Ces fonctionnalités comprennent l’intégration des webhooks, l’authentification des référentiels auprès d’Azure Active Directory et la prise en charge de la fonctionnalité de suppression.

* **Référentiel** : un registre contient un ou plusieurs référentiels, qui sont des groupes d’images de conteneur. Azure Container Registry prend en charge les espaces de noms de référentiel à plusieurs niveaux. Dans le cas des espaces de noms à plusieurs niveaux, vous pouvez regrouper des collections d’images liées à une application spécifique, ou une collection d’applications à des équipes opérationnelles ou de développement précises. Par exemple :

  * `myregistry.azurecr.io/aspnetcore:1.0.1` représente une image de l’entreprise.
  * `myregistry.azurecr.io/warrantydept/dotnet-build` représente une image utilisée pour créer des applications .NET, partagées par le service de garantie.
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` représente une image web, regroupée dans l’application des envois du client, détenue par le service de garantie.

* **Image** : stockée dans un référentiel, chaque image est une capture instantanée en lecture seule d’un conteneur Docker. Les registres de conteneur Azure peuvent inclure des images de Windows et Linux. Vous contrôlez les noms d’images pour tous les déploiements de votre conteneur. Utilisez des [commandes Docker](https://docs.docker.com/engine/reference/commandline/) standard pour envoyer les images dans un référentiel ou extraire une image d’un référentiel.

* **Conteneur** : un conteneur définit une application logicielle et ses dépendances encapsulées dans un système de fichiers complet, y compris le code, l’exécution, les outils système et les bibliothèques. Exécutez des conteneurs Docker basés sur des images Windows ou Linux que vous extrayez à partir d’un registre de conteneur. Les conteneurs en cours d’exécution sur un même ordinateur partagent le noyau du système d’exploitation. Les conteneurs Docker sont entièrement portables sur toutes les principales distributions Linux, macOS et Windows.

## <a name="next-steps"></a>Étapes suivantes
* [Créer un registre de conteneur à l’aide du portail Azure](container-registry-get-started-portal.md)
* [Créer un registre de conteneur à l’aide de l’interface de ligne de commande Azure](container-registry-get-started-azure-cli.md)
* [Effectuer un push de votre première image à l’aide de l’interface CLI Docker](container-registry-get-started-docker-cli.md)
* Pour générer un workflow de déploiement et d’intégration continu à l’aide de Visual Studio Team Services, Azure Container Service et Azure Container Registry, voir [Pipeline CI/CD complet pour déployer une application à conteneurs multiples sur Azure Container Service avec Docker Swarm à l’aide de Visual Studio Team Services](../container-service/dcos-swarm/container-service-docker-swarm-setup-ci-cd.md).