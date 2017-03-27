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
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 5fce9171dc249445d78cee248fb2a7ec62a3dabb
ms.lasthandoff: 03/11/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-visual-studio-team-services"></a>Créer une application web avec un déploiement continu à partir de Visual Studio Team Services

Cet exemple de script effectue les opérations suivantes à l’aide d’Azure CLI 2.0 : 

* Créer une application web dans Azure App Service dans la région Europe de l’Ouest Azure. 
* Déployer votre code d’application web à partir de Visual Studio Team Services.
* Afficher l’application web Azure déployée dans le navigateur.

## <a name="prerequisites"></a>Composants requis

* Exécutez `az login` pour vous connecter à Azure.
* Placez votre code d’application web dans un référentiel Visual Studio Team Services.
* Pour un référentiel Visual Studio Team Services dont vous êtes le propriétaire, [créez un jeton d’accès](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).

## <a name="create-app-sample"></a>Exemple de création d’application

[!code-azurecli[principal](../../../cli_scripts/app-service/deploy-vsts-continuous/deploy-vsts-continuous.sh?highlight=3-4 "Créer une application web avec un déploiement continu à partir de Visual Studio Team Services")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

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
