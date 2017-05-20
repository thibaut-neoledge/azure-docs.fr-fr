---
title: "Exemple de script Azure CLI - Créer une stratégie de basculement pour une haute disponibilité | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une stratégie de basculement pour une haute disponibilité"
services: cosmosdb
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 285e50ed5e0596bb18e2fb9124992af34da71f1e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-failover-policy-for-high-availability-using-the-azure-cli"></a>Créer une stratégie de basculement pour une haute disponibilité à l’aide d’Azure CLI

Cet exemple de script CLI crée un compte Azure Cosmos DB, puis le configure pour la haute disponibilité.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/cosmosdb/high-availability-cosmosdb-configure-failover/high-availability-cosmosdb-configure-failover.sh?highlight=23-27 "Créer une stratégie de basculement Azure Cosmos DB")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az cosmosdb create](/cli/azure/sql/server#create) | Crée un compte Azure Cosmos DB. |
| [az cosmosdb update](/cli/azure/cosmosdb#update) | Met à jour un compte Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous pouvez trouver des exemples supplémentaires de scripts CLI d’Azure Cosmos DB dans la [documentation d’Azure Cosmos DB](../cli-samples.md).

