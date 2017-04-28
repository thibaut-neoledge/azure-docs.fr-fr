---
title: "Exemple de script Azure CLI - Obtenir le nom d’hôte, les ports et les clés pour le Cache Redis Azure | Microsoft Docs"
description: "Exemple de script Azure CLI - Obtenir le nom d’hôte, les ports et les clés pour une instance du Cache Redis Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/14/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: cd9adc784bceb0fff5e7c2bbee2be0950c51c8f6
ms.lasthandoff: 04/15/2017

---

# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Obtenir le nom d’hôte, les ports et les clés pour le Cache Redis Azure

Ce scénario décrit comment récupérer le nom d’hôte, les ports et les clés utilisés pour se connecter à une instance du Cache Redis Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Cache Redis Azure")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour récupérer le nom d’hôte, les clés et les ports d’une instance du Cache Redis Azure. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#show) | Récupérer les détails d’une instance du Cache Redis Azure. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#list-keys) | Récupérer les clés d’accès pour une instance du Cache Redis Azure. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Pour des exemples supplémentaires de scripts CLI de Cache Redis Azure, voir la [documentation du Cache Redis Azure](../cli-samples.md).
