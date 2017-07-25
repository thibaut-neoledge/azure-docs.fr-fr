---
title: "Exemple de script Azure CLI - Mapper un domaine personnalisé à une Function App | Microsoft Docs"
description: "Exemple de script Azure CLI - Mapper un domaine personnalisé à une Function App dans Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 6fcea6d32f9dd25b0fafb4f895f60d8320ac9df8
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---
<a id="map-a-custom-domain-to-a-function-app" class="xliff"></a>

# Mapper un domaine personnalisé à une Function App

Cet exemple de script crée une Function App avec les ressources associées, puis la mappe à `www.<yourdomain>`. Pour mapper un domaine personnalisé, votre Function App doit être créée dans un plan App Service et non dans un plan de consommation. Azure Functions ne prend en charge le mappage d’un domaine personnalisé qu’avec un enregistrement A.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 


<a id="sample-script" class="xliff"></a>

## Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Mapper un domaine personnalisé à une Function App")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

<a id="script-explanation" class="xliff"></a>

## Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crée un compte de stockage requis par la Function App. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service requis pour mapper un domaine personnalisé. |
| [az functionapp create]() | Crée une Function App. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Mappe un domaine personnalisé à une Function App. |

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Functions dans la [documentation d’Azure Functions]().

