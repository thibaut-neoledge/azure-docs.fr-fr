---
title: "Script Azure CLI - Créer une instance SQL Database | Microsoft Docs"
description: "Exemple de script Azure CLI - Créer une base de données SQL avec l’interface CLI Azure"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/16/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a9ee545280d21744a2e91399c80ad1945bac5995
ms.lasthandoff: 03/17/2017

---

# <a name="create-a-single-sql-database-and-configure-a-firewall-rule-using-the-azure-cli"></a>Créer une instance SQL Database unique et configurer une règle de pare-feu à l’aide de l’interface de ligne de commande Azure

Cet exemple de script CLI crée une base de données SQL Azure et configure une règle de pare-feu au niveau du serveur. Une fois que le script a été exécuté avec succès, l’instance SQL Database est accessible à partir de tous les services Azure et l’adresse IP configurée. 

Si nécessaire, installez l’interface Azure CLI en suivant les instructions du [Guide d’installation Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), puis exécutez `az login` pour créer une connexion avec Azure.

Cet exemple fonctionne dans une interface d’interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Créer une instance SQL Database")]

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
| [az sql server create](/cli/azure/sql/server#create) | Crée un serveur logique qui héberge l’instance SQL Database. |
| [az sql server firewall create](/cli/azure/sql/server/firewall#create) | Crée une règle de pare-feu pour autoriser l’accès à toutes les instances SQL Database sur le serveur à partir de la plage d’adresses IP entrée. |
| [az sql db create](/cli/azure/sql/db#create) | Crée une instance SQL Database au sein du serveur logique. |
| [az group delete](/cli/azure/resource#delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI SQL Database sur la page [Documentation Azure SQL Database](../sql-database-cli-samples.md).


