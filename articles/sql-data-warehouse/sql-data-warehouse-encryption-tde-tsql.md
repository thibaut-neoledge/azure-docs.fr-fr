<properties
   pageTitle="Chiffrement transparent des données dans SQL Data Warehouse (T-SQL)| Microsoft Azure"
   description="Chiffrement transparent des données (TDE) dans SQL Data Warehouse (T-SQL)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Prise en main du chiffrement transparent des données (TDE)


> [AZURE.SELECTOR]
- [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
- [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## Autorisations requises

Pour activer le chiffrement transparent des données (TDE), vous devez être un administrateur ou un membre du rôle dbmanager.

## Activation du chiffrement

Pour activer le chiffrement transparent des données pour une instance SQL Data Warehouse, procédez comme suit :

1. Connectez-vous à la base de données *master* sur le serveur hébergeant la base de données à l'aide d'identifiants de connexion administrateurs ou membres du rôle **dbmanager** dans la base de données master
2. Exécutez l'instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## Désactivation du chiffrement

Pour désactiver le chiffrement transparent des données pour une instance SQL Data Warehouse, procédez comme suit :

1. Connectez-vous à la base de données *master* à l'aide d'identifiants de connexion administrateurs ou membres du rôle **dbmanager** dans la base de données master
2. Exécutez l'instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] Une instance SQL Data Warehouse suspendue doit reprendre avant que des modifications ne puissent être apportées aux paramètres de chiffrement transparent des données.

## Vérification de chiffrement

Pour vérifier l’état du chiffrement pour SQL Data Warehouse, procédez comme suit :

1. Connectez-vous à la base de données *master* ou d’instance à l'aide d'identifiants de connexion administrateurs ou membres du rôle **dbmanager** dans la base de données master
2. Exécutez l'instruction suivante pour chiffrer la base de données.

```sql
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

Un résultat de ```1``` indique une base de données chiffrée, ```0``` indique une base de données non chiffrée.

## DMV de chiffrement  

- [sys.databases][]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0928_2016-->