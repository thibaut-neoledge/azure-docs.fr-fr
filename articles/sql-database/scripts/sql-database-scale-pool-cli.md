---
title: "Script Azure CLI - Mettre à l’échelle un pool élastique | Microsoft Docs"
description: "Exemple de script Azure CLI - Mettre à l’échelle un pool de bases de données élastique"
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
ms.openlocfilehash: 9125fd5a780322ea5bde5a688fa381f3fe116ce6
ms.lasthandoff: 03/17/2017

---

# <a name="scale-an-elastic-pool-in-azure-sql-database-using-the-azure-cli"></a>Mettre à l’échelle un pool élastique dans Azure SQL Database à l’aide de l’interface de ligne de commande Azure

Cet exemple de script CLI crée des pools élastiques, déplace des bases de données regroupées et modifie les niveaux de performances. 

Si nécessaire, installez l’interface Azure CLI en suivant les instructions du [Guide d’installation Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), puis exécutez `az login` pour créer une connexion avec Azure.

Cet exemple fonctionne dans une interface d’interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Déplacer une base de données entre les pools")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un serveur logique, une instance SQL Database et des règles de pare-feu. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Crée un serveur logique qui héberge l’instance SQL Database. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | Crée un pool de bases de données élastique au sein du serveur logique. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Crée une instance SQL Database au sein du serveur logique. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pools#update) | Met à jour un pool de base de données élastique : dans cet exemple, modifie l’eDTU attribué. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI SQL Database sur la page [Documentation Azure SQL Database](../sql-database-cli-samples.md).

