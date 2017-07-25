---
title: "Exemple de script Azure CLI - Créer une application web avec un déploiement à partir de GitHub | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une application web avec un déploiement à partir de GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: sample
ms.topic: article
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 657aab37e41b81d8994fc333a2f582c8c23e27b7
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
# <a name="create-a-web-app-with-deployment-from-github"></a>Créer une application web avec un déploiement à partir de GitHub

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis déploie votre code d’application web dans un référentiel GitHub public (sans déploiement continu). Pour le déploiement GitHub avec déploiement continu, consultez [Créer une application web avec un déploiement continu à partir de GitHub](app-service-cli-continuous-deployment-github.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Créer une application web avec un déploiement à partir de GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script 

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#create) | Crée une application web Azure. |
| [az webapp deployment source config](https://docs.microsoft.com/cli/azure/webapp/deployment/source#config) | Associe une application web Azure à un référentiel Git ou Mercurial. |
| [az webapp browse](https://docs.microsoft.com/cli/azure/webapp#browse) | Ouvre une application web Azure dans un navigateur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).

