---
title: "Exemple de script Azure CLI - Mapper un domaine personnalisé à une application de fonction | Microsoft Docs"
description: "Exemple de script Azure CLI - Mapper un domaine personnalisé à une application de fonction dans Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/01/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 5a17830ad3b879382b7e82d435f1286c7ecdf7c5
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mapper un domaine personnalisé à une application de fonction

Cet exemple de script crée une application de fonction avec les ressources associées, puis la mappe à `www.<yourdomain>`. Pour mapper un domaine personnalisé, votre application de fonction doit être créée dans un plan App Service et non dans un plan de consommation. Azure Functions ne prend en charge le mappage d’un domaine personnalisé qu’avec un enregistrement A.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez utiliser Azure CLI 2.0 ou une version ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Mapper un domaine personnalisé à une application de fonction")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crée un compte de stockage requis par l’application de fonction. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service requis pour mapper un domaine personnalisé. |
| [az functionapp create]() | Crée une application de fonction. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Mappe un domaine personnalisé à une application de fonction. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Functions dans la [documentation d’Azure Functions]().

