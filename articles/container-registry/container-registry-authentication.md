---
title: Authentification avec un Registre de conteneurs Azure | Microsoft Docs
description: "Connexion à un Registre de conteneurs Azure à l’aide d’un compte d’administrateur ou d’un principal du service Azure Active Directory"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0433e22dc7722ef9c8edfaf949dbd9a9d8645e67
ms.openlocfilehash: acb04a648115c6de0b5699d404ebbf24a2edeb08
ms.lasthandoff: 03/02/2017

---
# <a name="authenticate-with-a-private-docker-container-registry"></a>S’authentifier avec un registre de conteneurs Docker
Pour utiliser des images de conteneur dans un Registre de conteneurs Azure, vous vous connectez à l’aide de la commande `docker login`. Vous pouvez vous connecter en utilisant un **[principal du service Azure Active Directory](../active-directory/active-directory-application-objects.md)** ou un **compte d’administrateur** spécifique à un registre. Cet article fournit des détails sur ces identités. 


> [!NOTE]
> Container Registry est actuellement en version préliminaire. En version préliminaire, les identités Azure Active Directory individuelles (qui activent le contrôle et l’accès par utilisateur) ne sont pas prises en charge pour l’authentification à l’aide d’un Registre de conteneurs. 
> 





## <a name="service-principal"></a>Principal du service

Vous pouvez [affecter un principal du service](container-registry-get-started-azure-cli.md#assign-a-service-principal) à votre registre et l’utiliser pour l’authentification Docker de base. L’utilisation d’un principal du service est recommandée dans la plupart des scénarios. Fournissez le mot de passe et l’ID d’application du principal du service à la commande `docker login`, comme indiqué dans l’exemple suivant :

```
docker login myregistry-contoso.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Une fois que vous êtes connecté, Docker met les informations d’identification en cache. Vous n’avez donc pas besoin de vous souvenir de l’ID d’application.

> [!TIP]
> Si vous le souhaitez, vous pouvez régénérer le mot de passe d’un principal du service en exécutant la commande `az ad sp reset-credentials`.
> 


Les principaux du service autorisent [l’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md) à un registre. Les rôles disponibles sont Lecteur (accès en extraction uniquement), Collaborateur (envoyer et extraire) et Propriétaire (envoyer, extraire et attribuer des rôles à d’autres utilisateurs). Vous pouvez affecter plusieurs principaux du service à un registre, ce qui vous permet de définir l’accès pour différents utilisateurs ou applications. Les principaux du service activent également la connectivité « headless » (sans périphérique de contrôle) à un registre dans des scénarios de développeur ou DevOps, tels que ceux qui suivent :

  * Déploiements de conteneurs à partir d’un registre vers des systèmes d’orchestration, y compris DC/OS, Docker Swarm et Kubernetes. Vous pouvez également extraire des registres de conteneurs vers des services Azure connexes tels que [Container Service](../container-service/index.md), [App Service](../app-service/index.md), [Batch](../batch/index.md) et [Service Fabric](../service-fabric/index.md).
  
  * Solutions d’intégration et de déploiement en continu (comme Visual Studio Team Services ou Jenkins) qui créent des images de conteneur et les envoient vers un registre.
  
  



## <a name="admin-account"></a>Compte d’administrateur
Un compte d’administrateur est créé automatiquement pour chaque registre que vous créez. Le compte est désactivé par défaut, mais vous pouvez l’activer et gérer les informations d’identification, par exemple à l’aide du [portal](container-registry-get-started-portal.md#manage-registry-settings) ou des [commandes d’Azure CLI 2.0](container-registry-get-started-azure-cli.md#manage-admin-credentials). Si le compte est activé, vous pouvez transmettre le nom d’utilisateur et le mot de passe à la commande `docker login` pour une authentification de base dans le registre. Par exemple :

```
docker login myregistry-contoso.azurecr.io -u myAdminName -p myPassword
```

> [!IMPORTANT]
> Le compte d’administrateur est conçu pour permettre à un seul utilisateur d’accéder au registre, principalement à des fins de test. Il n’est pas recommandé de partager les informations d’identification du compte d’administrateur avec d’autres utilisateurs. Tous les utilisateurs apparaissent sous la forme d’un seul utilisateur dans le registre. La modification ou la désactivation de ce compte désactive l’accès au registre pour tous les utilisateurs qui utilisent les informations d’identification. 
> 


### <a name="next-steps"></a>Étapes suivantes
* [Transmettre votre première image à l’aide de l’interface de ligne de commande (CLI) Docker](container-registry-get-started-docker-cli.md).
* Pour plus d’informations sur l’authentification dans la version préliminaire du Registre de conteneurs, consultez le [billet de blog](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/). 



