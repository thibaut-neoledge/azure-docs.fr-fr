---
title: "Exemples de CLI Azure pour créer un serveur de base de données unique pour MySQL unique et configurer une règle de pare-feu | Microsoft Docs"
description: "Cet exemple de script CLI crée un serveur Azure Database pour MySQL et configure une règle de pare-feu au niveau du serveur."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.custom: sample
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fa961f123dddc2e6243d1efbd1d72b994179eb79
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-mysql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Créer un serveur MySQL et configurer une règle de pare-feu à l’aide de l’interface de ligne de commande Azure
Cet exemple de script CLI crée un serveur Azure Database pour MySQL et configure une règle de pare-feu au niveau du serveur. Une fois que le script s’exécute correctement, le serveur MySQL est accessible par tous les services Azure et l’adresse IP configurée.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script
```bash
#!/bin/bash

# Create a resource group
az group create \
--name myresource \
--location westus

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mysqlserver4demo \
--resource-group myresource \
--location westus \
--admin-user myadmin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az mysql server firewall-rule create \
--resource-group myresource \
--server mysqlserver4demo \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
 Une fois l’exemple de script exécuté, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées.
```azurecli
az group delete --name myresource
```
## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| **Commande** | **Remarques** |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| az mysql server create | Crée un serveur MySQL qui héberge les bases de données. |
| az mysql server firewall create | Crée une règle de pare-feu pour autoriser l’accès au serveur et aux bases de données qui s’y trouvent à partir de la plage d’adresses IP entrée. |
| [az group delete](https://docs.microsoft.com/en-us/cli/azure/resource#delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
[Exemples de CLI Azure pour Azure Database pour MySQL](../sample-scripts-azure-cli.md)

