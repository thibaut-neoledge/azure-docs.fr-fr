---
title: "Script CLI Azure - Créer une instance d’Azure Database pour PostgreSQL | Microsoft Docs"
description: "Exemple de script CLI - crée un serveur Azure Database pour PostgreSQL et configure une règle de pare-feu au niveau du serveur."
services: postgresql
author: salonisonpal
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
ms.openlocfilehash: 0f61358aa655a44ac7982c80eb1d796667c47bf4
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="create-an-azure-database-for-postgresql-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Créer un serveur Azure Database pour PostgreSQL et configurer une règle de pare-feu à l’aide de l’interface de ligne de commande Azure
Cet exemple de script CLI crée un serveur Azure Database pour PostgreSQL et configure une règle de pare-feu au niveau du serveur. Une fois que le script a été exécuté avec succès, le serveur PostgreSQL est accessible à partir de tous les services Azure et l’adresse IP configurée.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exemple de script
```azurecli
#!/bin/bash

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a PostgreSQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az postgres server create \
--name mypgserver-20170401 \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--performance-tier Basic \
--compute-units 50 \

# Configure a firewall rule for the server
# The ip address range that you want to allow to access your server
az postgres server firewall-rule create \
--resource-group myresourcegroup \
--server mypgserver-20170401 \
--name AllowIps \
--start-ip-address 0.0.0.0 \
--end-ip-address 255.255.255.255

# Default database ‘postgres’ gets created on the server.
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).
- Vous trouverez d’autres exemples de scripts CLI Azure Database pour PostgreSQL dans la [documentation Azure Database pour PostgreSQL](../sample-scripts-azure-cli.md).

