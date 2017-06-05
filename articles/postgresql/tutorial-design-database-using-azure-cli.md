---
title: "Concevoir votre première base de données Azure pour PostgreSQL avec Azure CLI | Microsoft Docs"
description: "Ce didacticiel montre comment concevoir votre première base de données Azure pour PostgreSQL à l’aide de l’interface Azure CLI."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.custom: tutorial
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fbac141243f0f36aab1a2ef9f28b4e107fd2d390
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>Concevoir votre première base de données Azure pour PostgreSQL avec Azure CLI 
Dans ce didacticiel, vous allez utiliser l’interface Azure CLI (interface de ligne de commande) et d’autres utilitaires pour apprendre à :
> [!div class="checklist"]
> * Créer une base de données Azure pour PostgreSQL
> * Configurer le pare-feu du serveur
> * Utiliser l’utilitaire [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) pour créer une base de données
> * Charger les exemples de données
> * Données de requête
> * Mettre à jour des données
> * Restaurer des données

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.
```azurecli
az login
```

Si vous possédez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource existe ou est facturée. Sélectionnez un ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account#set).
```azurecli
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. L’exemple suivant crée un groupe de ressources nommé `myresourcegroup` à l’emplacement `westus`.
```azurecli
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Créer un serveur Azure Database pour PostgreSQL

Créez un [serveur Azure Database pour PostgreSQL](overview.md) avec la commande **az sql server create**. Un serveur contient un groupe de bases de données gérées en tant que groupe. 

L’exemple suivant crée un serveur nommé `mypgserver-20170401` dans votre groupe de ressources `myresourcegroup` avec l’identifiant d’administrateur serveur `mylogin`. Le nom du serveur correspond au nom DNS et doit ainsi être globalement unique dans Azure. Remplacez `<server_admin_password>` par votre propre valeur.
```azurecli
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.

Par défaut, la base de données **postgres** est créée sous le serveur. La base de données [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) est une base de données par défaut destinée aux utilisateurs, utilitaires et applications tierces. 


## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Créez une règle de pare-feu au niveau du serveur Azure PostgreSQL avec la commande **az postgres server firewall-rule create**. Une règle de pare-feu au niveau du serveur permet à une application externe, comme [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) ou [PgAdmin](https://www.pgadmin.org/), de se connecter à votre serveur via le pare-feu du service Azure PostgreSQL. 

Vous pouvez définir une règle de pare-feu qui couvre une plage d’adresses IP pour pouvoir vous connecter à partir de votre réseau. L’exemple suivant utilise **az postgres server firewall-rule create** pour créer une règle de pare-feu `AllowAllIps` pour une plage d’adresses IP. Pour ouvrir toutes les adresses IP, utilisez 0.0.0.0 comme adresse IP de début et 255.255.255.255 comme adresse de fin.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Le serveur Azure PostgreSQL communique sur le port 5432. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 5432 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 5432.
>

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte ainsi que des informations d’identification pour l’accès.
```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

Le résultat est au format JSON. Prenez note des valeurs **administratorLogin** et **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Utiliser psql pour se connecter à la base de données Azure Database pour PostgreSQL

Si PostgreSQL est installé sur votre ordinateur client, vous pouvez utiliser une instance locale de [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) pour vous connecter à un serveur Azure PostgreSQL. Nous allons maintenant utiliser l’utilitaire de ligne de commande psql pour nous connecter au serveur Azure pour PostgreSQL.

1. Exécutez la commande psql suivante pour vous connecter à un serveur Azure Database pour PostgreSQL
```bash
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Par exemple, la commande suivante se connecte à la base de données par défaut appelée **postgres** sur le serveur PostgreSQL **mypgserver-20170401.postgres.database.azure.com** à l’aide des informations d’identification d’accès. Saisissez le `<server_admin_password>` que vous avez choisi à l’invite de mot de passe.
  
  ```bash
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  Une fois que vous êtes connecté au serveur, créez une base de données vide à l’invite.
```bash
CREATE DATABASE mypgsqldb;
```

3.  À l’invite, exécutez la commande suivante pour basculer la connexion sur la base de données **mypgsqldb** nouvellement créée :
```bash
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Créer des tables dans la base de données
Maintenant que vous savez comment vous connecter à la base de données Azure pour PostgreSQL, nous pouvons aborder certaines tâches de base.

Tout d’abord, nous pouvons créer une table et la charger avec des données. Nous allons créer une table qui assure le suivi des informations d’inventaire.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Vous pouvez localiser cette nouvelle table dans la liste des tables en tapant :
```bash
\dt
```

## <a name="load-data-into-the-tables"></a>Charger des données dans les tables
Maintenant que nous disposons d’une table, nous pouvons y insérer des données. Dans la fenêtre d’invite de commandes ouverte, exécutez la requête suivante pour insérer des lignes de données.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Vous avez maintenant chargé deux lignes de données dans la table que vous avez créée précédemment.

## <a name="query-and-update-the-data-in-the-tables"></a>Interroger et mettre à jour les données des tables
Exécutez la requête suivante pour récupérer des informations à partir de la table de base de données. 
```sql
SELECT * FROM inventory;
```

Vous pouvez également mettre à jour les données des tables.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La ligne est mise à jour en conséquence lorsque vous récupérez les données.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurer une version antérieure d’une base de données
Imaginez que vous avez supprimé une table par inadvertance. Il s’agit de quelque chose que vous ne pouvez pas récupérer facilement. La base de données Azure pour PostgreSQL vous permet de revenir à n’importe quel point dans le temps (dans la limite de 7 jours (De base) et de 35 jours (Standard)), puis d’effectuer une restauration vers un nouveau serveur. Vous pouvez alors utiliser ce nouveau serveur pour récupérer les données supprimées. Les étapes suivantes restaurent le serveur à l’état dans lequel il était avant l’ajout de la table.

Pour effectuer la restauration, vous avez besoin des informations suivantes :
- **Point de restauration :** sélectionnez un point dans le temps avant la modification du serveur. Doit être supérieure ou égale à la plus ancienne valeur de sauvegarde de la base de données source.
- **Serveur cible :** spécifiez un nouveau nom de serveur sur lequel vous souhaitez effectuer la restauration
- **Serveur source :** indiquez le nom du serveur à partir duquel vous voulez effectuer la restauration
- **Emplacement :** vous ne pouvez pas sélectionner la région. Par défaut, elle est identique à celle du serveur source
- **Niveau tarifaire :** vous ne pouvez pas modifier cette valeur lors de la restauration d’un serveur. Elle est identique à celle du serveur source. 

```azurecli
az postgres server restore --resource-group myResourceGroup --name mypgserver-20170401-restored --restore-point-in-time "2017-04-13 03:10" --source-server-name mypgserver-20170401
```

Pour restaurer le serveur [à un point dans le temps](./howto-restore-server-portal.md) avant la suppression des tables. La restauration d’un serveur à un autre point dans le temps crée un serveur en double par rapport au serveur d’origine, à condition qu’il se trouve au sein de la période de rétention de votre [niveau de service](./concepts-service-tiers.md).

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à utiliser l’interface Azure CLI (interface de ligne de commande) et d’autres utilitaires pour :
> [!div class="checklist"]
> * Créer une base de données Azure pour PostgreSQL
> * Configurer le pare-feu du serveur
> * Utiliser l’utilitaire [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) pour créer une base de données
> * Charger les exemples de données
> * Données de requête
> * Mettre à jour des données
> * Restaurer des données

À présent, découvrez comment utiliser le portail Azure pour effectuer des tâches similaires. Lisez le didacticiel [Concevoir votre première base de données Azure pour PostgreSQL à l’aide du portail Azure](tutorial-design-database-using-azure-portal.md).

