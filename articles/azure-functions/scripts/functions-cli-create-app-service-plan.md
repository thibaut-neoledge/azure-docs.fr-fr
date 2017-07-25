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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 04/11/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 40c3fa6fa6c07d59e4bf55531e116ba50aa92b91
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Créer une Function App dans un plan App Service

Cet exemple de script crée une Function App Azure, qui constitue un conteneur pour vos fonctions. La Function App est créée à l’aide d’un plan App Service dédié, ce qui signifie que vos ressources serveur sont toujours disponibles.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

Ce script crée une Function App d’Azure en utilisant un [plan App Service](../functions-scale.md#app-service-plan) dédié.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Créer une fonction Azure Functions sur un plan App Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Chaque commande du tableau renvoie à une documentation spécifique. Ce script utilise les commandes suivantes :

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crée un compte de stockage Azure. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appserviceplan#create) | Crée un plan App Service. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#delete) | Crée une Function App Azure. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).

