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
	ms.date="02/26/2016"
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
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;
GO;
```

## Reprendre la migration des données

### SQL Server Management Studio permet de reprendre la migration des données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table compatible Stretch pour laquelle vous souhaitez reprendre la migration des données.

2.  Cliquez avec le bouton droit et sélectionnez **Stretch**, puis sélectionnez **Reprendre**.

### Utiliser Transact-SQL pour reprendre la migration des données
Exécutez la commande ci-dessous.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;
```

## Voir aussi

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0316_2016-->