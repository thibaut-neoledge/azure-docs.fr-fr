<properties
   pageTitle="Créer un entrepôt SQL Data Warehouse avec TSQL | Microsoft Azure"
   description="Découvrez comment créer un entrepôt Azure SQL Data Warehouse avec TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL).

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Cet article vous explique comment créer une base de données SQL Data Warehouse à l’aide de Transact SQL (T-SQL).

## Composants requis
Vérifiez que les conditions préalables suivantes sont remplies avant de commencer :

- **Compte Azure **: consultez [Évaluation gratuite d’Azure][] ou [Crédits Azure MSDN][] pour créer un compte.
- **Serveur Azure SQL Server V12** : consultez [Créer un serveur logique de base de données SQL Azure avec le Portail Azure][] ou [Créer un serveur logique de base de données SQL Azure avec PowerShell][].
- **Nom de groupe de ressources** : utilisez le même groupe de ressources que votre serveur Azure SQL Server V12 ou consultez [groupes de ressources][] pour créer un groupe de ressources.
- **Visual Studio avec SQL Server Data Tools** : pour obtenir des instructions d’installation, consultez [Installation de Visual Studio et SSDT][].

> [AZURE.NOTE] La création d’un entrepôt SQL Data Warehouse peut entraîner un nouveau service facturable. Voir [Tarification de SQL Data Warehouse][] pour plus d’informations sur la tarification.

## Créer une base de données avec Visual Studio

Si vous débutez avec Visual Studio, consultez l’article [Se connecter à SQL Data Warehouse avec Visual Studio][]. Pour commencer, ouvrez l’Explorateur d’objets SQL Server dans Visual Studio et connectez-vous au serveur qui hébergera votre base de données SQL Data Warehouse. Une fois que vous êtes connecté, vous pouvez créer un entrepôt SQL Data Warehouse en exécutant la commande SQL suivante sur la base de données **master**. Cette commande crée la base de données MySqlDwDb avec un objectif de service DW400 et permet à la base de données d’atteindre une taille maximale de 10 To.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Créez une base de données avec sqlcmd.

Vous pouvez également exécuter la même commande avec sqlcmd. Pour cela, exécutez la commande suivante à partir de l’invite de commandes.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Le paramètre `MAXSIZE` peut être compris entre 250 Go et 240 To. Le paramètre `SERVICE_OBJECTIVE` peut être compris entre DW100 et DW2000 [DWU][]. Pour obtenir la liste de toutes les valeurs valides, consultez la documentation MSDN pour [CREATE DATABASE][]. Les paramètres MAXSIZE et SERVICE\_OBJECTIVE peuvent également être modifiés avec une commande T-SQL [ALTER DATABASE][]. Modifiez le paramètre SERVICE\_OBJECTIVE avec prudence car cela entraîne un redémarrage des services, qui annule toutes les requêtes en cours. La modification du paramètre MAXSIZE ne redémarre pas les services car il s’agit d’une simple opération de métadonnées.

## Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les données d’exemple][] ou découvrir comment [développer][], [charger][] ou [migrer][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: ./sql-data-warehouse-get-started-provision.md
[Se connecter à SQL Data Warehouse avec Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[migrer]: ./sql-data-warehouse-overview-migrate.md
[développer]: ./sql-data-warehouse-overview-develop.md
[charger]: ./sql-data-warehouse-overview-load.md
[charger les données d’exemple]: ./sql-data-warehouse-get-started-load-sample-databases.md
[Créer un serveur logique de base de données SQL Azure avec le Portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Créer un serveur logique de base de données SQL Azure avec PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[groupes de ressources]: ../azure-portal/resource-group-portal.md
[Installation de Visual Studio et SSDT]: ./sql-data-warehouse-install-visual-studio.md


<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Tarification de SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Évaluation gratuite d’Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédits Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0706_2016-->