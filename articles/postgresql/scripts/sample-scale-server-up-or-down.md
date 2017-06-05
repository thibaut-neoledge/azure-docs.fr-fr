---
title: "Script CLI Azure - Mettre à l’échelle Azure Database pour PostgreSQL | Microsoft Docs"
description: "Exemple de script Azure CLI - Mettre à l’échelle le serveur Azure Database pour PostgreSQL vers un autre niveau de performances après interrogation des métriques."
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
ms.date: 04/30/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 36134ca1a899c25ab896577815ce9108cae0d563
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Surveiller et mettre à l’échelle d’un seul serveur PostgreSQL à l’aide de la CLI Azure
Cet exemple de script CLI met à l’échelle un serveur Azure Database pour PostgreSQL vers un nouveau niveau de performance après l’analyse des métriques. 

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

# Monitor usage metrics - Compute
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names compute_consumption_percent \
--time-grain PT1M

# Monitor usage metrics - Storage
az monitor metrics list \
--resource-id "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401" \
--metric-names storage_used \
--time-grain PT1M

# Scale up the server to provision more Compute Units within the same Tier
az postgres server update \
--resource-group myresourcegroup \
--name mypgserver-20170401 \
--compute-units 100
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.
```azurecli
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).
- Vous trouverez d’autres exemples de scripts CLI Azure Database pour PostgreSQL dans la [documentation Azure Database pour PostgreSQL](../sample-scripts-azure-cli.md).
- Pour plus d’informations sur la mise à l’échelle, consultez [Niveaux de service](../concepts-service-tiers.md) et [Unités de calcul et unités de stockage](../concepts-compute-unit-and-storage.md).
