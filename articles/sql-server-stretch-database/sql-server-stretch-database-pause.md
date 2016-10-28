<properties
	pageTitle="Suspension et reprise de la migration des données (Stretch Database) | Microsoft Azure"
	description="Découvrez comment suspendre ou reprendre la migration des données vers Azure."
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
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Suspension et reprise de la migration des données (Stretch Database)

Pour suspendre ou reprendre la migration des données vers Azure, sélectionnez **Stretch** pour une table dans SQL Server Management Studio, puis sélectionnez **Suspendre** pour suspendre la migration des données ou **reprendre** pour reprendre la migration de données. Vous pouvez également utiliser Transact-SQL pour suspendre ou reprendre la migration des données.

Suspendez la migration des données sur différentes tables pour résoudre les problèmes sur le serveur local ou optimiser la bande passante réseau disponible.

## Suspension de la migration des données

### SQL Server Management Studio permet de suspendre la migration des données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table compatible Stretch pour laquelle vous souhaitez suspendre la migration des données.

2.  Cliquez avec le bouton droit et sélectionnez **Stretch**, puis sélectionnez **Suspendre**.

### Utiliser Transact-SQL pour suspendre la migration des données
Exécutez la commande ci-dessous.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## Reprendre la migration des données

### SQL Server Management Studio permet de reprendre la migration des données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table compatible Stretch pour laquelle vous souhaitez reprendre la migration des données.

2.  Cliquez avec le bouton droit et sélectionnez **Stretch**, puis sélectionnez **Reprendre**.

### Utiliser Transact-SQL pour reprendre la migration des données
Exécutez la commande ci-dessous.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## Vérifier si la migration est active ou en pause

### Utilisez SQL Server Management Studio pour vérifier si la migration est active ou en pause
Dans SQL Server Management Studio, ouvrez **Stretch Database Monitor** (Surveiller Stretch Database) et vérifiez la valeur de la colonne **Migration State** (État de la migration). Pour plus d’informations, voir [Surveillance et dépannage de la migration de données](sql-server-stretch-database-monitor.md).

### Utiliser Transact-SQL pour vérifier si la migration est active ou en pause
Interrogez l’affichage catalogue **sys.remote\_data\_archive\_tables** et vérifiez la valeur de la colonne **is\_migration\_paused**. Pour plus d’informations, consultez [sys.remote\_data\_archive\_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## Voir aussi

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx) [Surveillance et dépannage de la migration de données](sql-server-stretch-database-monitor.md)

<!---HONumber=AcomDC_0622_2016-->