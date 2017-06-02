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
ms.date: 04/09/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: cd7ab0bbe92fa32d23a841b0b17bee8510f6b406
ms.contentlocale: fr-fr
ms.lasthandoff: 05/15/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>Mapper un domaine personnalisé à une Function App

Cet exemple de script crée une Function App avec les ressources associées, puis la mappe à `www.<yourdomain>`. Pour mapper un domaine personnalisé, votre Function App doit être créée dans un plan App Service et non dans un plan de consommation. Azure Functions ne prend en charge le mappage d’un domaine personnalisé qu’avec un enregistrement A.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Mapper un domaine personnalisé à une Function App")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crée un compte de stockage requis par la Function App. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crée un plan App Service requis pour mapper un domaine personnalisé. |
| [az functionapp create]() | Crée une Function App. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Mappe un domaine personnalisé à une Function App. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI Functions dans la [documentation d’Azure Functions]().

