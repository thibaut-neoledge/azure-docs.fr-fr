---
title: "Exemple de script Azure CLI - Créer une Function App dans un plan App Service | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une Function App dans un plan App Service"
services: functions
documentationcenter: functions
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.service: functions
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0d887999d1118e498293fb13b9612828d914b0ae
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-function-app-in-an-app-service-plan"></a>Créer une Function App dans un plan App Service

Cet exemple de script crée une Function App Azure, qui constitue un conteneur pour vos fonctions. La Function App est créée à l’aide d’un plan App Service dédié, ce qui signifie que vos ressources serveur sont toujours disponibles.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

Exemple de création d’application

[!code-azurecli[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Créer une fonction Azure Functions sur un plan App Service")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application App Service et toutes les ressources associées.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az appserviceplan create](https://docs.microsoft.com/cli/azure/appserviceplan#create) | Crée un plan App Service. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#delete) | Crée une Function App Azure. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).
