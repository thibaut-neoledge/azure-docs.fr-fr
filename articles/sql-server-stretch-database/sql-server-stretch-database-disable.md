<properties
	pageTitle="Désactiver Stretch Database et récupérer des données distantes | Microsoft Azure"
	description="Découvrez comment désactiver Stretch Database pour une table et éventuellement récupérer des données distantes."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Désactiver Stretch Database et récupérer des données distantes

Pour désactiver Stretch Database pour une table, sélectionnez **Stretch** pour une table dans SQL Server Management Studio. Sélectionnez ensuite l’une des options suivantes.

-   **Désactiver | Récupérer les données à partir d’Azure**. Copier les données distantes pour la table à partir d’Azure vers SQL Server, puis désactiver Stretch Database pour la table. Cette opération entraîne des coûts de transfert de données et ne peut pas être annulée.

-   **Désactiver | Laisser les données dans Azure**. Désactiver Stretch Database pour la table. Abandonner les données distantes pour la table dans Azure.

Une fois que vous avez désactivé Stretch Database pour une table, la migration des données s’arrête et les résultats de requête n’incluent plus les résultats de la table distante.

Vous pouvez également utiliser Transact-SQL pour désactiver Stretch Database pour une table ou une base de données.

Si vous souhaitez simplement suspendre la migration des données, consultez [Suspendre et reprendre Stretch Database](sql-server-stretch-database-pause.md).

## Désactiver Stretch Database pour une table

### Utiliser SQL Server Management Studio pour désactiver Stretch Database pour une table

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table pour laquelle vous souhaitez désactiver Stretch Database.

2.  Cliquez avec le bouton droit et sélectionnez **Stretch**, puis sélectionnez l’une des options suivantes.

    -   **Désactiver | Récupérer les données à partir d’Azure**. Copier les données distantes pour la table à partir d’Azure vers SQL Server, puis désactiver Stretch Database pour la table. Cette commande ne peut pas être annulée.

        Cette opération entraîne des coûts de transfert de données. Pour plus d’informations, consultez [Tarification - Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

        Une fois que toutes les données distantes ont été copiées à partir d’Azure vers SQL Server, Stretch est désactivé pour la table.

    -   **Désactiver | Laisser les données dans Azure**. Désactiver Stretch Database pour la table. Abandonner les données distantes pour la table dans Azure.

        L’abandon des données distantes et la désactivation de Stretch ne suppriment pas les données distantes. Si vous souhaitez les supprimer, vous devez supprimer la table distante à l’aide du portail de gestion Azure.

### Utiliser Transact-SQL pour désactiver Stretch Database pour une table

-   Pour désactiver Stretch pour une table et copier les données distantes de la table à partir d’Azure SQL Database vers SQL Server, exécutez la commande suivante. Cette commande ne peut pas être annulée.

    ```tsql
    ALTER TABLE <table name>
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;
    ```
    Cette opération entraîne des coûts de transfert de données. Pour plus d’informations, consultez [Tarification - Transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

    Une fois que toutes les données distantes ont été copiées à partir d’Azure vers SQL Server, Stretch est désactivé pour la table.

-   Pour désactiver Stretch pour une table et abandonner les données distantes, exécutez la commande suivante.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```
    L’abandon des données distantes et la désactivation de Stretch ne suppriment pas les données distantes. Si vous souhaitez les supprimer, vous devez supprimer la table distante à l’aide du portail de gestion Azure.

## Désactiver Stretch Database pour une base de données
Avant de pouvoir désactiver Stretch Database pour une base de données, vous devez désactiver Stretch Database sur chacune des tables Stretch de la base de données.

### Utiliser SQL Server Management Studio pour désactiver Stretch Database pour une base de données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la base de données pour laquelle vous souhaitez désactiver Stretch Database.

2.  Cliquez avec le bouton droit et sélectionnez **Tâches**, sélectionnez **Stretch**, puis **Désactiver**.

### Utiliser Transact-SQL pour désactiver Stretch Database pour une base de données
Exécutez la commande ci-dessous.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

## Supprimer une base de données Stretch
La suppression d’une base de données qui était activée pour Stretch Database supprime la base de données locale, mais pas les données distantes. Si vous souhaitez supprimer les données distantes, vous devez supprimer la base de données distante à l’aide du portail de gestion Azure.

## Voir aussi
[Options SET d’ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx) [Suspendre et reprendre Stretch Database](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0302_2016-->