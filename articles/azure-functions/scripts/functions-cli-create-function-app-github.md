---
title: "Créer une Function App et déployer le code de fonction à partir de GitHub | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une Function App et déployer le code de fonction à partir de GitHub"
services: functions
keywords: 
author: syntaxc4
ms.author: cfowler
ms.date: 04/27/2017
ms.topic: sample
ms.service: functions
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: d67e85f91c80efe464fceb1105243bedfba83a0f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
<a id="create-a-function-app-and-deploy-function-code-from-github" class="xliff"></a>

# Créer une Function App et déployer le code de fonction à partir de GitHub

Cet exemple de script crée une Function App à l’aide du [plan de consommation](../functions-scale.md#consumption-plan) avec les ressources associées et déploie votre code de fonction à partir d’un référentiel GitHub public (sans déploiement continu). En ce qui concerne la livraison continue du code de fonction à partir de GitHub, consultez la page [Créer une Function App et déployer en continu à partir de GitHub](functions-cli-create-function-app-github-continuous.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

<a id="sample-script" class="xliff"></a>

## Exemple de script

Cet exemple crée une Function App Azure et déploie le code de fonction à partir de GitHub.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-github/deploy-function-app-with-function-github.sh?highlight=3 "Créer une Function App avec un déploiement à partir de GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

<a id="script-explanation" class="xliff"></a>

## Explication du script

Chaque commande du tableau renvoie à une documentation spécifique. Ce script utilise les commandes suivantes :

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service. |
| [az functionapp create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crée une Function App Azure. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Associe une Function App à un référentiel Git ou Mercurial. |

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Azure Functions dans la [documentation d’Azure Functions](../functions-cli-samples.md).

