---
title: "Configuration des paramètres de service dans Azure Database pour PostgreSQL | Microsoft Docs"
description: "Décrit comment configurer les paramètres de service dans Azure Database pour PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e46e3d588782ec0f2ef89c58001e997b8fc2910
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Personnalisation des paramètres de configuration du serveur à l’aide de l’interface de ligne de commande Azure
Vous pouvez répertorier, afficher et mettre à jour les paramètres de configuration d’un serveur Azure PostgreSQL à l’aide de l’interface de ligne de commande (Azure CLI). Toutefois, un seul sous-ensemble de configurations de moteur est exposé au niveau du serveur et peut être modifié. 

## <a name="prerequisites"></a>Composants requis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un serveur et une base de données [Création d’une base de données Azure POUR PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Un utilitaire en ligne de commande [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) installé

## <a name="list-server-configuration-parameters-for-azure-postgresql-server"></a>Répertorier les paramètres de configuration de serveur pour le serveur Azure PostgreSQL
Pour obtenir une liste de tous les paramètres modifiables d’un serveur Azure PostgreSQL et leurs valeurs, exécutez la commande suivante **az postgres server configuration** comme indiqué ici :
> az postgres server configuration list --resource-group <resource group name> --server <server name>

Par exemple, vous pouvez répertorier les paramètres de configuration de serveur du serveur Azure PostgreSQL **mypgserver.postgres.database.azure.com** du groupe de ressources **myresourcegroup**.
```azurecli
az postgres server configuration list --resource-group myresourcegroup --server mypgserver
```
## <a name="show-server-configuration-parameter-details"></a>Affichage des détails des paramètres de configuration du serveur
Pour afficher les détails d’un paramètre de configuration spécifique pour un serveur Azure PostgreSQL, exécutez la commande **az postgres server configuration**.
```azurecli
az postgres server configuration show --name <configuration name>
   --resource-group <resource group name> --server <server name>
```
Par exemple, vous pouvez afficher les détails du paramètre de configuration de serveur **log\_min\_messages** pour le serveur Azure PostgreSQL **mypgserver.postgres.database.azure.com** du groupe de ressources **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages --resource-group myresourcegroup --server mypgserver
```
## <a name="modify-server-configuration-parameter-value"></a>Modification de la valeur de paramètre de configuration du serveur
Vous pouvez modifier la valeur de certains paramètres de configuration d’un serveur Azure PostgreSQL, et cela met à jour la valeur de configuration sous-jacente du moteur de serveur PostgreSQL. Pour mettre à jour la valeur de configuration, exécutez la commande **az postgres server configuration** suivante. 
```azurecli
az postgres server configuration update --name <configuration name>
   --resource-group <resource group name> --server <server name>[--value]
```
Par exemple, vous pouvez mettre à jour les détails du paramètre de configuration de serveur **log\_min\_messages** pour le serveur Azure PostgreSQL **mypgserver.postgres.database.azure.com** du groupe de ressources **myresourcegroup. **
```azurecli
az postgres server configuration show --name log\_min\_messages 
   --resource-group myresourcegroup --server mypgserver --value INFO
```
Si vous souhaitez réinitialiser la valeur d’un paramètre de configuration, il vous suffit de ne pas définir le paramètre --value facultatif : le service applique alors la valeur par défaut. Dans l’exemple ci-dessus, elle peut se présenter comme suit :
```azurecli
az postgres server configuration show --name log\_min\_messages
   --resource-group myresourcegroup --server mypgserver
```
Cette opération réinitialise la configuration du paramètre log\_min\_messages à la valeur par défaut WARNING. Pour plus d’informations sur la configuration du serveur et les valeurs autorisées, consultez la documentation PostgreSQL à la rubrique [Configuration du serveur](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Étapes suivantes
- Pour configurer et accéder aux journaux du serveur, consultez la rubrique [Journaux de serveur dans Azure Database pour PostgreSQL](concepts-server-logs.md)
