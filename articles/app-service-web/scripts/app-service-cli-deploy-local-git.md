---
title: "Exemple de script Azure CLI - Créer une application web et déployer du code à partir d’un référentiel Git local | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une application web et déployer du code à partir d’un référentiel Git local"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: b55da57afae5951447bb2cca81e772c71eec6e35
ms.contentlocale: fr-fr
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Créer une application web et déployer du code à partir d’un référentiel Git local

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis déploie votre code d’application web dans un référentiel Git local.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[principal](../../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh?highlight=3-5 "Créer une application web et déployer du code à partir d’un référentiel Git")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crée une application web Azure. |
| [az appservice web deployment user set](https://docs.microsoft.com/cli/azure/appservice/web/deployment/user#set) | Définit les informations d’identification de déploiement au niveau des comptes pour App Service. |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config-local-git) | Crée une configuration de contrôle source pour un référentiel Git local. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Ouvre une application web Azure dans un navigateur. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI App Service dans la [documentation relative à Azure App Service](../app-service-cli-samples.md).

