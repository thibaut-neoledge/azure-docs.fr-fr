---
title: "Interface de ligne de commande Azure : Créer et interroger une base de données SQL unique | Microsoft Docs"
description: "Découvrez comment créer un serveur logique SQL Database, une règle de pare-feu de niveau serveur et des bases de données à l’aide de l’interface de ligne de commande Azure."
keywords: "didacticiel sur la base de données sql, créer une base de données sql"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: cli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8322e46f462b6c940f9808411d99aa1cee0beea5
ms.lasthandoff: 03/15/2017

---

# <a name="create-and-query-a-single-azure-sql-database-with-the-azure-cli"></a>Créer et interroger une base de données SQL Azure unique avec l’interface de ligne de commande Azure

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide explique comment utiliser l’interface de ligne de commande Azure pour déployer une base de données SQL Azure.

Avant de commencer, assurez-vous que l’interface de ligne de commande Azure est installée. Pour plus d’informations, consultez le [Guide d’installation de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Création d'un serveur logique

Créez un serveur logique avec la commande [az sql server create](/cli/azure/sql/server#create). L’exemple suivant illustre la création d’un serveur nommé de façon aléatoire dans votre groupe de ressources avec un identifiant d’administrateur nommé `ServerAdmin` et un mot de passe `ChangeYourAdminPassword1`. Remplacez les valeurs prédéfinies par ce que vous souhaitez.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Configurer une règle de pare-feu du serveur

Créez une règle de pare-feu au niveau du serveur à l’aide de la commande [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create). Une règle de pare-feu au niveau du serveur permet à une application externe, telle que SQL Server Management Studio ou l’utilitaire SQLCMD, de se connecter à une base de données SQL via le pare-feu du service SQL Database. L’exemple suivant illustre la création d’une règle de pare-feu pour une plage d’adresses prédéfinie qui, ici, est l’intégralité de la plage d’adresses IP possible. Remplacez ces valeurs prédéfinies par les valeurs de votre adresse IP externe ou de votre plage d’adresses IP. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server"></a>Créer une base de données dans le serveur

Créez une base de données sur le serveur à l’aide de la commande [az sql db create](/cli/azure/sql/db#create). L’exemple suivant illustre la création d’une base de données vide nommée `mySampleDatabase`. Remplacez cette valeur prédéfinie par ce que vous souhaitez.

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --service-objective S0
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer toutes les ressources créées par ce démarrage rapide, exécutez la commande suivante :

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- Pour vous connecter et envoyer des requêtes à l’aide de SQL Server Management Studio, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).
- Pour vous connecter à l’aide de Visual Studio, consultez [Se connecter à la base de données SQL avec Visual Studio](sql-database-connect-query.md).
- Pour une présentation technique de SQL Database, consultez [Définition de la base de données SQL Présentation de SQL Database](sql-database-technical-overview.md).

