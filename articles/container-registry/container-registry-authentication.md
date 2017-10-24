---
title: Authentification avec un Registre de conteneurs Azure | Microsoft Docs
description: "Connexion à un Registre de conteneurs Azure à l’aide d’un compte d’administrateur ou d’un principal du service Azure Active Directory"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d7d2ae0e9b1f7850332d151d78a4a5fdb013777
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>S’authentifier avec un registre de conteneurs Docker
Pour utiliser des images de conteneur dans un Registre de conteneurs Azure, vous vous connectez à l’aide de la commande `docker login`. Vous pouvez vous connecter en utilisant un **[principal du service Azure Active Directory](../active-directory/active-directory-application-objects.md)** ou un **compte d’administrateur** spécifique à un registre. Cet article fournit des détails sur ces identités.

## <a name="service-principal"></a>Principal du service

Vous pouvez affecter un principal du service à votre registre et l’utiliser pour l’authentification Docker de base. L’utilisation d’un principal du service est recommandée dans la plupart des scénarios. Fournissez le mot de passe et l’ID d’application du principal du service à la commande `docker login`, comme indiqué dans l’exemple suivant :

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Une fois que vous êtes connecté, Docker met les informations d’identification en cache. Vous n’avez donc pas besoin de vous souvenir de l’ID d’application.

> [!TIP]
> Si vous le souhaitez, vous pouvez régénérer le mot de passe d’un principal du service en exécutant la commande `az ad sp reset-credentials`.
>

Les principaux du service autorisent [l’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md) à un registre. Les rôles disponibles sont les suivants :
  * Lecteur (extraction).
  * Contributeur (extraction et envoi).
  * Propriétaire (extraction, envoi et attribution de rôles à d’autres utilisateurs).

L’accès anonyme n’est pas disponible sur les registres de conteneur Azure. Pour des images publiques, vous pouvez utiliser [Docker Hub](https://docs.docker.com/docker-hub/).

Vous pouvez affecter plusieurs principaux du service à un registre, ce qui vous permet de définir l’accès pour différents utilisateurs ou applications. Les principaux du service activent également la connectivité « headless » (sans périphérique de contrôle) à un registre dans des scénarios de Développeur ou DevOps, tels que les exemples suivants :

  * Déploiements de conteneurs à partir d’un registre vers des systèmes d’orchestration, y compris DC/OS, Docker Swarm et Kubernetes. Vous pouvez également extraire des registres de conteneurs vers des services Azure connexes tels que [Container Service](../container-service/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.md), [Service Fabric](/azure/service-fabric/) et autres.

  * Solutions d’intégration et de déploiement en continu (comme Visual Studio Team Services ou Jenkins) qui créent des images de conteneur et les envoient vers un registre.


## <a name="admin-account"></a>Compte d’administrateur
Un compte d’administrateur est créé automatiquement pour chaque registre que vous créez. Le compte est désactivé par défaut, mais vous pouvez l’activer et gérer les informations d’identification, par exemple à l’aide du [portal](container-registry-get-started-portal.md#create-a-container-registry) ou des [commandes d’Azure CLI 2.0](container-registry-get-started-azure-cli.md#create-a-container-registry). Chaque compte d’administrateur reçoit deux mots de passe qui peuvent être régénérés. Les deux mots de passe vous permettent de maintenir des connexions au Registre à en utilisant un mot de passe tandis que vous régénérez l’autre. Si le compte est activé, vous pouvez transmettre le nom d’utilisateur et chaque mot de passe à la commande `docker login` pour une authentification de base auprès du Registre. Par exemple :

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

> [!IMPORTANT]
> Le compte d’administrateur est conçu pour permettre à un seul utilisateur d’accéder au registre, principalement à des fins de test. Il n’est pas recommandé de partager les informations d’identification du compte d’administrateur avec d’autres utilisateurs. Tous les utilisateurs apparaissent sous la forme d’un seul utilisateur dans le registre. La modification ou la désactivation de ce compte désactive l’accès au registre pour tous les utilisateurs qui utilisent les informations d’identification.
>

### <a name="next-steps"></a>Étapes suivantes
* [Transmettre votre première image à l’aide de l’interface de ligne de commande (CLI) Docker](container-registry-get-started-docker-cli.md).
* Pour plus d’informations sur l’authentification dans la version préliminaire du Registre de conteneurs, consultez le [billet de blog](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/).
