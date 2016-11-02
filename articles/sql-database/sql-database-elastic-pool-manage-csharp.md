<properties
    pageTitle="Surveiller et gérer un pool de bases de données élastique avec C# | Microsoft Azure"
    description="Utilisez les techniques de développement de bases de données C# pour gérer un pool de bases de données élastique Azure SQL Database."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-c&#x23;"></a>Surveiller et gérer un pool de bases de données élastique avec C&#x23; 

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Découvrez comment gérer un [pool de bases de données élastique](sql-database-elastic-pool.md) à l’aide de C&#x23;. 

>[AZURE.NOTE] Nombre des nouvelles fonctionnalités de SQL Database ne sont prises en charge que si vous utilisez le [modèle de déploiement Azure Resource Manager](../resource-group-overview.md), c’est pourquoi vous devez toujours utiliser la toute dernière **bibliothèque de base de données SQL Azure pour .NET ([docs](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Les anciennes [bibliothèques basées sur des modèles de déploiement classique](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) sont prises en charge pour la compatibilité ascendante uniquement, donc nous vous recommandons d’utiliser les bibliothèques Resource Manager plus récentes.

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Un pool élastique (le pool que vous voulez gérer). Pour créer un pool, voir [Créer un pool de bases de données élastiques avec C#](sql-database-elastic-pool-create-csharp.md).
- Visual Studio. Pour obtenir une copie gratuite de Visual Studio, consultez la page [Téléchargements Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique

Vous pouvez déplacer une base de données autonome à l’intérieur ou hors d’un pool.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Répertorier les bases de données dans un pool élastique

Pour récupérer toutes les bases de données dans un pool, appelez la méthode [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Modifier les paramètres de performances d’un pool

Récupérez les propriétés du pool existantes. Modifiez les valeurs et exécutez la méthode CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latence des opérations du pool élastique

- En général, le processus de modification du nombre minimal d’eDTU par base de données ou du nombre maximal d’eDTU par base de données prend cinq minutes au maximum.
- La durée de modification de la taille du pool (eDTU) dépend de la taille combinée de toutes les bases de données du pool. Ce processus prend en moyenne 90 minutes au maximum, pour chaque tranche de 100 Go. Par exemple, si l’espace total de toutes les bases de données du pool est égal à 200 Go, une opération de modification du nombre d’eDTU par pool prend 3 heures au maximum.




## <a name="additional-resources"></a>Ressources supplémentaires

- [Codes d’erreur SQL pour les applications clientes SQL Database : erreur de connexion à la base de données et autres problèmes](sql-database-develop-error-messages.md).
- [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [API de gestion des ressources Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Créer un pool de base de données élastique avec C#](sql-database-elastic-pool-create-csharp.md)
- [Quand utiliser un pool de base de données élastique ?](sql-database-elastic-pool-guidance.md)
- Consultez [Montée en charge avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md): utilisez les outils de base de données élastique pour monter en charge, déplacer des données, exécuter des requêtes ou créer des transactions.




<!--HONumber=Oct16_HO2-->


