<properties
   pageTitle="Mise en route avec TSQL et le chiffrement transparent des données (TDE) pour SQL Data Warehouse | Microsoft Azure"
   description="Mise en route avec TSQL et le chiffrement transparent des données (TDE) pour SQL Data Warehouse"
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
   ms.date="08/29/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Prise en main du chiffrement transparent des données (TDE)


> [AZURE.SELECTOR]
- [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Détection de menaces](sql-data-warehouse-security-threat-detection.md)
- [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
- [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [Vue d’ensemble de l’audit](sql-data-warehouse-auditing-overview.md)
- [Audit des clients de niveau inférieur](sql-data-warehouse-auditing-downlevel-clients.md)


La fonction de chiffrement transparent des données (TDE) d’Azure SQL Data Warehouse protège le système contre toute menace d’activité malveillante, en effectuant un chiffrement et un déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions au repos, sans exiger de modification de l’application.

Le chiffrement transparent des données chiffre le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. Dans la base de données SQL, la clé de chiffrement de base de données est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur de base de données SQL. Microsoft alterne automatiquement ces certificats au moins tous les 90 jours. L’algorithme de chiffrement utilisé par SQL Data Warehouse est AES-256. Pour obtenir une description générale du chiffrement transparent des données, consultez [Chiffrement transparent des données (TDE)].

##Activation du chiffrement

Pour activer le chiffrement transparent des données pour SQL Data Warehouse, procédez comme suit :

1. Connectez-vous à la base de données *master* sur le serveur hébergeant la base de données à l'aide d'identifiants de connexion administrateurs ou membres du rôle **dbmanager** dans la base de données master
2. Exécutez l'instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##Désactivation du chiffrement

Pour désactiver le chiffrement transparent des données pour SQL Data Warehouse, procédez comme suit :

1. Connectez-vous à la base de données *master* à l'aide d'identifiants de connexion administrateurs ou membres du rôle **dbmanager** dans la base de données master
2. Exécutez l'instruction suivante pour chiffrer la base de données.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

Remarque : un SQL Data Warehouse suspendu doit reprendre avant que des modifications ne puissent être apportées aux paramètres de chiffrement transparent des données.

##Vérification de chiffrement

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

##DMV de chiffrement  

- [sys.databases][]
- [sys.dm\_pdw\_nodes\_database\_encryption\_keys][]


<!--Anchors-->
[Chiffrement transparent des données (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm\_pdw\_nodes\_database\_encryption\_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0907_2016-->