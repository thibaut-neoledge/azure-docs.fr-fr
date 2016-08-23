<properties
	pageTitle="Désactiver Stretch Database et récupérer des données distantes | Microsoft Azure"
	description="Découvrez comment désactiver Stretch Database pour une table et éventuellement récupérer des données distantes."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="douglasl"/>

# Désactiver Stretch Database et récupérer des données distantes

Pour désactiver Stretch Database pour une table, sélectionnez **Stretch** pour une table dans SQL Server Management Studio. Sélectionnez ensuite l’une des options suivantes.

-   **Désactiver| Bring data back from Azure**. Copy the remote data for the table from Azure back to SQL Server, then disable Stretch Database for the table. This operation incurs data transfer costs, and it can't be canceled.

-   **Désactiver| Leave data in Azure**. Disable Stretch Database for the table.  Abandon the remote data for the table in Azure.

Vous pouvez également utiliser Transact-SQL pour désactiver Stretch Database pour une table ou une base de données.

Une fois que vous avez désactivé Stretch Database pour une table, la migration des données s’arrête et les résultats de requête n’incluent plus les résultats de la table distante.

Si vous souhaitez simplement suspendre la migration des données, consultez [Suspendre et reprendre Stretch Database](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] La désactivation de Stretch Database pour une table ou une base de données ne supprime pas l'objet distant. Si vous souhaitez supprimer la table distante ou la base de données distante, vous devez le faire à l’aide du portail de gestion Azure. Les objets distants continuent d’entraîner des coûts Azure jusqu’à leur suppression. Pour plus d’informations, consultez [Tarification SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Désactiver Stretch Database pour une table

### Utiliser SQL Server Management Studio pour désactiver Stretch Database pour une table

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table pour laquelle vous souhaitez désactiver Stretch Database.

2.  Cliquez avec le bouton droit et sélectionnez **Stretch**, puis sélectionnez l’une des options suivantes.

    -   **Désactiver| Bring data back from Azure**. Copy the remote data for the table from Azure back to SQL Server, then disable Stretch Database for the table. This command can't be canceled.

        >   [AZURE.NOTE] Copying the remote data for the table from Azure back to SQL Server incurs data transfer costs. For more info, see [Data Transfers Pricing Details](https://azure.microsoft.com/pricing/details/data-transfers/).

        After all the remote data has been copied from Azure back to SQL Server, Stretch is disabled for the table.

    -   **Désactiver| Leave data in Azure**. Disable Stretch Database for the table.  Abandon the remote data for the table in Azure.

    >   [AZURE.NOTE] La désactivation de Stretch Database pour une table ne supprime pas les données distantes ni la table distante. Si vous souhaitez supprimer la table distante, vous devez le faire à l’aide du portail de gestion Azure. La table distante continue d’entraîner des coûts Azure jusqu’à sa suppression. Pour plus d’informations, consultez [Tarification SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Utiliser Transact-SQL pour désactiver Stretch Database pour une table

-   Pour désactiver Stretch Database pour une table et copier les données distantes de la table à partir d’Azure vers SQL Server, exécutez la commande suivante. Une fois que toutes les données distantes ont été copiées à partir d’Azure vers SQL Server, Stretch est désactivé pour la table.

    Cette commande ne peut pas être annulée.

    ```tsql
	USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] La copie des données distantes pour la table d'Azure vers SQL Server entraîne des coûts de transfert de données. Pour plus d’informations, consultez [Tarification - Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

-   Pour désactiver Stretch pour une table et abandonner les données distantes, exécutez la commande suivante.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] La désactivation de Stretch Database pour une table ne supprime pas les données distantes ni la table distante. Si vous souhaitez supprimer la table distante, vous devez le faire à l’aide du portail de gestion Azure. La table distante continue d’entraîner des coûts Azure jusqu’à sa suppression. Pour plus d’informations, consultez [Tarification SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Désactiver Stretch Database pour une base de données
Avant de pouvoir désactiver Stretch Database pour une base de données, vous devez désactiver Stretch Database sur chacune des tables Stretch de la base de données.

### Utiliser SQL Server Management Studio pour désactiver Stretch Database pour une base de données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la base de données pour laquelle vous souhaitez désactiver Stretch Database.

2.  Cliquez avec le bouton droit et sélectionnez **Tâches**, **Stretch**, puis **Désactiver**.

>   [AZURE.NOTE] La désactivation de Stretch Database pour une base de données ne supprime pas la base de données distante. Si vous souhaitez supprimer la base de données distante, vous devez le faire à l’aide du portail de gestion Azure. La base de données distante continue d’entraîner des coûts Azure jusqu’à sa suppression. Pour plus d’informations, consultez [Tarification SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Utiliser Transact-SQL pour désactiver Stretch Database pour une base de données
Exécutez la commande ci-dessous.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] La désactivation de Stretch Database pour une base de données ne supprime pas la base de données distante. Si vous souhaitez supprimer la base de données distante, vous devez le faire à l’aide du portail de gestion Azure. La base de données distante continue d’entraîner des coûts Azure jusqu’à sa suppression. Pour plus d’informations, consultez [Tarification SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Voir aussi

[Options SET d’ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Suspendre et reprendre Stretch Database](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0810_2016-->