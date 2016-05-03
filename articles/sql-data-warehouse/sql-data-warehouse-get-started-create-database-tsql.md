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
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL).

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Cet article vous explique comment créer une base de données SQL Data Warehouse à l’aide de Transact SQL (TSQL).

## Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Un serveur logique SQL V12. Vous aurez besoin d’un serveur SQL V12 pour créer l’entrepôt SQL Data Warehouse. Si vous ne disposez pas d’un serveur SQL logique V12, consultez la section **Configurer et créer un serveur** de l’article [Créer un entrepôt SQL Data Warehouse à partir du Portail Azure][].
- Visual Studio. Pour obtenir une copie gratuite de Visual Studio, consultez la page [Téléchargements Visual Studio][].


> [AZURE.NOTE] La création d’un entrepôt SQL Data Warehouse peut entraîner un nouveau service facturable. Voir [Tarification de SQL Data Warehouse ][] pour plus d’informations sur la tarification.

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

Les paramètres **MAXSIZE** et **SERVICE\_OBJECTIVE** spécifient l’espace maximal que la base de données peut utiliser sur le disque et les ressources de calcul allouées à votre instance Data Warehouse. L’objectif de service est essentiellement une allocation de ressources d’UC et de mémoire mise à l’échelle de façon linéaire avec la taille de DWU.

Le paramètre MAXSIZE peut être compris entre 250 Go et 60 To. L’objectif de service peut être compris entre DW100 et DW2000. Pour obtenir la liste complète des valeurs valides des paramètres MAXSIZE et SERVICE\_OBJECTIVE, consultez les sections sur l’instruction [CREATE DATABASE][] dans la documentation MSDN. Les paramètres MAXSIZE et SERVICE\_OBJECTIVE peuvent également être modifiés avec une commande T-SQL [ALTER DATABASE][]. Modifiez le paramètre SERVICE\_OBJECTIVE avec prudence car cela entraîne un redémarrage des services, qui annule toutes les requêtes en cours. La modification du paramètre MAXSIZE n’implique pas cette précaution. En effet, il s’agit d’une simple opération de métadonnées.

## Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les données d’exemple][] ou découvrir comment [développer][], [charger][] ou [migrer][].

<!--Article references-->
[Créer un entrepôt SQL Data Warehouse à partir du Portail Azure]: sql-data-warehouse-get-started-provision.md
[Se connecter à SQL Data Warehouse avec Visual Studio]: sql-data-warehouse-get-started-connect.md
[migrer]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md
[charger]: sql-data-warehouse-overview-load.md
[charger les données d’exemple]: sql-data-warehouse-get-started-manually-load-samples.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Tarification de SQL Data Warehouse ]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Téléchargements Visual Studio]: https://www.visualstudio.com/downloads/download-visual-studio-vs

<!---HONumber=AcomDC_0427_2016-->