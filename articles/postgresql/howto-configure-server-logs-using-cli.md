---
title: "Configuration et accès aux journaux du serveur pour PostgreSQL à l’aide de la ligne de commande Azure | Microsoft Docs"
description: "Décrit comment configurer et accéder aux journaux du serveur dans la base de données Azure pour PostgreSQL."
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
ms.openlocfilehash: dc047e6d5194035586931b3cacc4d5cd07f6cfc2
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Configuration et accès aux journaux du serveur à l’aide de la ligne de commande Azure
Vous pouvez répertorier et télécharger les journaux d’erreurs du serveur Azure PostgreSQL à l’aide de l’interface de ligne de commande (CLI Azure). Toutefois, l’accès aux journaux de transactions n’est pas pris en charge. 

## <a name="prerequisites"></a>Composants requis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Un [serveur Azure Database pour PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Un utilitaire en ligne de commande [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) installé

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Configuration de la journalisation pour Azure Database pour PostgreSQL
Vous pouvez configurer le serveur afin d’accéder aux journaux de requêtes et aux journaux d’erreurs. Les journaux d’erreurs peuvent contenir des informations sur le nettoyage automatique, connexion et les points de contrôle.
1. Activation de la journalisation
2. Mise à jour de l\_’instruction de journalisation et de l’instruction de\_ \_durée minimale\_de journalisation pour activer la journalisation des requêtes
3. Mise à jour de la période de rétention

Consultez la rubrique [Personnalisation des paramètres de configuration du serveur](howto-configure-server-parameters-using-cli.md) pour plus d’informations.

## <a name="list-logs-for-azure-postgresql-server"></a>Liste des journaux du serveur Azure PostgreSQL
Pour répertorier les fichiers journaux disponibles pour votre serveur, exécutez la commande **az postgres server-logs** comme indiqué dans l’exemple suivant :
```azurecli
az postgres server-logs list --resource-group <resource group name> --server <server name> [ --file-last-written --filename-contains --max-file-size ]
```
Par exemple, vous pouvez répertorier les fichiers journaux du serveur Azure PostgreSQL **mypgserver.postgres.database.azure.com** du groupe de ressources **myresourcegroup** et les diriger vers un fichier texte appelé **liste\_fichiers\_journaux.txt. **
```azurecli
az postgres server-logs list --resource-group **myresourcegroup** --server **mypgserver** > log\_files\_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Téléchargement des journaux localement à partir du serveur
Vous pouvez également télécharger des fichiers journaux individuels pour votre serveur Azure PostgreSQL. 
```azurecli
az postgres server-logs download –name <log file name> --resource-group <resource group name> --server <server name>
```
Cet exemple télécharge le fichier journal pour le serveur Azure PostgreSQL **mypgserver.postgres.database.azure.com** du groupe de ressources **myresourcegroup** sur votre environnement local.
```azurecli
az postgres server-logs download --name 20170414-mypgserver-postgresql.log --resource-group **myresourcegroup** --server **mypgserver**
```
## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur les journaux de serveur, consultez la rubrique [Journaux de serveur dans la base de données Azure pour PostgreSQL](concepts-server-logs.md)
- Pour plus d’informations sur les paramètres du serveur, consultez la rubrique [Personnalisation des paramètres de configuration de serveur à l’aide de l’interface de ligne de commande Azure](howto-configure-server-parameters-using-cli.md)
