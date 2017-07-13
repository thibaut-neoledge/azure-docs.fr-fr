---
title: "Script CLI Azure - Obtenir la chaîne de connexion à Azure Cosmos DB pour les applications MongoDB | Microsoft Docs"
description: "Exemple de script CLI Azure - Obtenir la chaîne de connexion à Azure Cosmos DB pour les applications MongoDB"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 6faee797ff309fef2b748fa5ac7522a2c090c37f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

<a id="get-an-azure-cosmos-db-connection-string-for-mongodb-apps-using-the-azure-cli" class="xliff"></a>

# Obtenir une chaîne de connexion Azure Cosmos DB pour les applications MongoDB à l’aide de la CLI Azure

Cet exemple obtient une chaîne de connexion Azure Cosmos DB pour les applications MongoDB à l’aide de la CLI Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

<a id="sample-script" class="xliff"></a>

## Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-get-mongodb-connection-string/secure-cosmosdb-get-mongodb-connection-string.sh?highlight=36-39 "Obtenir une chaîne de connexion Azure Cosmos DB pour les applications MongoDB")]

<a id="clean-up-deployment" class="xliff"></a>

## Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

<a id="script-explanation" class="xliff"></a>

## Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | Met à jour un compte Azure Cosmos DB. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb/list-connection-strings) | Obtient la chaîne de connexion pour le compte.|
| [az group delete](/cli/azure/resource#delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI d’Azure Cosmos DB dans la [documentation d’Azure Cosmos DB](../cli-samples.md).

