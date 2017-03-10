---
title: "Exemple de script Azure CLI - Créer une application web avec un déploiement continu à partir de Visual Studio Team Services | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une application web avec un déploiement continu à partir de Visual Studio Team Services"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 389d3bd3-cd8e-4715-a3a1-031ec061d385
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: d54e58da5755009710bdcd11eaf845d6ce4f76d4
ms.openlocfilehash: 915227599645186ed11165affd378f2de282c7ea
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Créer une application web avec un déploiement continu à partir de Visual Studio Team Services

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis configure un déploiement continu à partir d’un référentiel Visual Studio Team Services (VSTS). 

Avant d’exécuter ce script, vérifiez les points suivants :

- Une connexion avec Azure a été créée à l’aide de la commande `az login`.
- Le code d’application se trouve dans un référentiel Visual Studio Team Services public ou privé dont vous êtes propriétaire.
- Vous avez [créé un jeton d’accès dans VSTS](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).

Cet exemple fonctionne dans une interface d’interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="create-app-sample"></a>Exemple de création d’application

[!code-azurecli[principal](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Créer une application web avec un déploiement continu à partir de Visual Studio Team Services")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crée une application web Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associe une application web Azure à un référentiel Git ou Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Ouvre une application web Azure dans un navigateur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).
