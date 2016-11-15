---
title: "Créer un entrepôt SQL Data Warehouse avec TSQL | Microsoft Docs"
description: "Découvrez comment créer un entrepôt Azure SQL Data Warehouse avec TSQL"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 443e74834a7181560e812ce31db460ef2c4ff911


---
# <a name="create-a-sql-data-warehouse-database-by-using-transactsql-tsql"></a>Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [Portail Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

Cet article vous explique comment créer un entrepôt SQL Data Warehouse à l’aide de T-SQL.

## <a name="prerequisites"></a>Composants requis
Pour commencer, vous avez besoin des éléments suivants : 

* **Compte Azure** : consultez [Évaluation gratuite d’Azure][Évaluation gratuite d’Azure] ou [Crédits Azure MSDN][Crédits Azure MSDN] pour créer un compte.
* **Serveur SQL Azure** : pour plus de détails, consultez [Créer un serveur logique Azure SQL Database avec le portail Azure][Créer un serveur logique Azure SQL Database avec le portail Azure] ou [Créer un serveur logique Azure SQL Database avec PowerShell][Créer un serveur logique Azure SQL Database avec PowerShell].
* **Groupe de ressources** : utilisez le même groupe de ressources que votre serveur Azure SQL Server ou consultez [Créer un groupe de ressources][Créer un groupe de ressources].
* **Environnement d’exécution de T-SQL** : vous pouvez utiliser [Visual Studio][Installation de Visual Studio et/ou SSDT], [sqlcmd][sqlcmd] ou [SSMS][SSMS] pour exécuter T-SQL.

> [!NOTE]
> La création d’un entrepôt SQL Data Warehouse peut entraîner un nouveau service facturable.  Voir [Tarification de SQL Data Warehouse][Tarification de SQL Data Warehouse] pour plus d’informations sur la tarification.
> 
> 

## <a name="create-a-database-with-visual-studio"></a>Créer une base de données avec Visual Studio
Si vous débutez avec Visual Studio, consultez l’article [Interroger Azure SQL Data Warehouse (sqlcmd) (Visual Studio)][Interroger Azure SQL Data Warehouse (sqlcmd) (Visual Studio)].  Pour commencer, ouvrez l’Explorateur d’objets SQL Server dans Visual Studio et connectez-vous au serveur qui hébergera votre base de données SQL Data Warehouse.  Une fois que vous êtes connecté, vous pouvez créer un entrepôt SQL Data Warehouse en exécutant la commande SQL suivante sur la base de données **master** .  Cette commande crée la base de données MySqlDwDb avec un objectif de service DW400 et permet à la base de données d’atteindre une taille maximale de 10 To.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Créer une base de données avec sqlcmd
Vous pouvez également exécuter la même commande avec sqlcmd. Pour cela, exécutez la commande suivante à partir de l’invite de commandes.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Le classement par défaut est COLLATE SQL_Latin1_General_CP1_CI_AS.  Le paramètre `MAXSIZE` peut être compris entre 250 Go et 240 To.  Le paramètre `SERVICE_OBJECTIVE` peut être compris entre DW100 et DW2000 [DWU][DWU].  Pour obtenir la liste de toutes les valeurs valides, consultez la documentation MSDN pour [CREATE DATABASE][CREATE DATABASE].  Les paramètres MAXSIZE et SERVICE_OBJECTIVE peuvent être modifiés avec une commande T-SQL [ALTER DATABASE][ALTER DATABASE].  Le classement d’une base de données ne peut pas être modifié après la création.   Modifiez le paramètre SERVICE_OBJECTIVE avec prudence car la modification de DWU entraîne un redémarrage des services, qui annule toutes les requêtes en cours.  La modification du paramètre MAXSIZE ne redémarre pas les services car il s’agit d’une simple opération de métadonnées.

## <a name="next-steps"></a>Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les exemples de données][charger les exemples de données] ou découvrir comment [développer][développer], [charger][charger] ou [migrer][migrer].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Création d’une base de données SQL Data Warehouse à partir du portail Azure]: sql-data-warehouse-get-started-provision.md
[Interroger Azure SQL Data Warehouse (sqlcmd) (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrer]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md
[charger]: sql-data-warehouse-overview-load.md
[charger les données d’exemple]: sql-data-warehouse-load-sample-databases.md
[Créer un serveur logique de base de données SQL Azure avec le Portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Créer un serveur logique de base de données SQL Azure avec PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Créer un groupe de ressources]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installation de Visual Studio et/ou SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Tarification de SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Évaluation gratuite d’Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédits Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


