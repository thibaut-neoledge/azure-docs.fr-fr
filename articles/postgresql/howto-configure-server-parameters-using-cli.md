---
title: "Configuration des paramètres de service dans Azure Database pour PostgreSQL | Microsoft Docs"
description: "Cet article décrit comment configurer les paramètres du service dans Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande (CLI) Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 9a575148a05843bef7524eff61407b377292ca3b
ms.contentlocale: fr-fr
ms.lasthandoff: 06/16/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personnalisation des paramètres de configuration du serveur à l’aide de l’interface de ligne de commande Azure
Vous pouvez répertorier, afficher et mettre à jour les paramètres de configuration d’un serveur Azure PostgreSQL à l’aide de l’interface de ligne de commande (Azure CLI). Toutefois, un seul sous-ensemble de configurations de moteur est exposé au niveau du serveur et peut être modifié. 

## <a name="prerequisites"></a>Composants requis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un serveur et une base de données [Création d’une base de données Azure POUR PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Installer l’utilitaire de ligne de commande [Azure CLI 2.0](/cli/azure/install-azure-cli) ou utiliser Azure Cloud Shell dans le navigateur.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Répertorier les paramètres de configuration de Base de données Azure pour le serveur PostgreSQL
Pour répertorier tous les paramètres modifiables dans un serveur, ainsi que leurs valeurs, exécutez la commande [az postgres server configuration list](/cli/azure/postgres/server/configuration#list).

Par exemple, vous pouvez répertorier les paramètres de configuration du serveur **mypgserver-20170401.postgres.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="show-server-configuration-parameter-details"></a>Affichage des détails des paramètres de configuration du serveur
Pour afficher les détails d’un paramètre de configuration particulier pour un serveur, exécutez la commande [az postgres server configuration show](/cli/azure/postgres/server/configuration#show).

Cet exemple affiche les détails du paramètre de configuration de serveur **log\_min\_messages** pour le serveur **mypgserver-20170401.postgres.database.azure.com** du groupe de ressources **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="modify-server-configuration-parameter-value"></a>Modification de la valeur de paramètre de configuration du serveur
Vous pouvez également modifier la valeur d’un paramètre de configuration de serveur, ce qui a pour effet de mettre à jour la valeur de configuration sous-jacente du moteur du serveur PostgreSQL. Pour mettre à jour la configuration, exécutez la commande [az postgres server configuration set](/cli/azure/postgres/server/configuration#set). 

Pour mettre à jour le paramètre de configuration de serveur **log\_min\_messages** pour le serveur **mypgserver-20170401.postgres.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401 --value INFO
```
Si vous souhaitez réinitialiser la valeur d’un paramètre de configuration, il vous suffit de ne pas définir le paramètre `--value` facultatif. Le service applique alors la valeur par défaut. Dans l’exemple ci-dessus, elle peut se présenter comme suit :
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
Cette opération réinitialise la configuration **log\_min\_messages** à la valeur par défaut **WARNING**. Pour plus d’informations sur la configuration du serveur et les valeurs autorisées, consultez la documentation PostgreSQL à la rubrique [Configuration du serveur](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Étapes suivantes
- Pour configurer et accéder aux journaux du serveur, consultez la rubrique [Journaux de serveur dans Azure Database pour PostgreSQL](concepts-server-logs.md)

