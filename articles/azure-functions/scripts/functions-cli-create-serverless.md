---
title: "Exemple de script Azure CLI - Créer une Function App pour une exécution sans serveur | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une Function App pour une exécution sans serveur"
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
ms.openlocfilehash: 37046967bd5ab0d797d1c66690db7200fb4805e2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="create-a-function-app-for-serverless-execution"></a>Créer une Function App pour une exécution sans serveur

Cet exemple de script crée une Function App Azure, qui constitue un conteneur pour vos fonctions. La Function App est créée à l’aide du [plan de consommation](../functions-scale.md#consumption-plan), ce qui est idéal pour les charges de travail sans serveur, pilotées par les événements.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemple de script

Ce script crée une Function App Azure à l’aide du [plan de consommation](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption/create-function-app-consumption.sh "Créer une fonction Azure Functions sur un plan de consommation")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Chaque commande du tableau renvoie à une documentation spécifique. Ce script utilise les commandes suivantes :

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](/cli/azure/storage/account#create) | Crée un compte de stockage Azure. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#create) | Crée une fonction Azure Functions. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).

