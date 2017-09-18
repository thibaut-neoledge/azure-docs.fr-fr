---
title: "Exemple de script Azure CLI - Obtenir les détails d’un cache Redis Azure | Microsoft Docs"
description: "Exemple de script Azure CLI - Obtenir les détails d’un cache Redis Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 0927508117bda67b62a188f89a2d2e151fe02a5a
ms.contentlocale: fr-fr
ms.lasthandoff: 09/09/2017

---

# <a name="get-details-of-an-azure-redis-cache"></a>Obtenir les détails d’un cache Redis Azure

Dans ce scénario, vous allez apprendre à récupérer les détails d’une instance de cache Redis Azure, y compris son état d’approvisionnement.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Cache Redis Azure")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour récupérer les détails d’une instance du cache Redis Azure. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Récupérer les détails d’une instance du cache Redis Azure. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous pouvez trouver des exemples supplémentaires de scripts CLI de cache Redis Azure dans la [documentation du cache Redis Azure](../cli-samples.md).
