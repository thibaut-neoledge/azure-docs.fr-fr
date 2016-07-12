<properties
   pageTitle="Création d'un entrepôt SQL Data Warehouse à l'aide de Powershell | Microsoft Azure"
   description="Création de SQL Data Warehouse à l'aide de Powershell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/01/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Création de SQL Data Warehouse à l'aide de Powershell

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Configuration requise
Vérifiez que les conditions préalables suivantes sont remplies avant de commencer :

- **Compte Azure **: consultez [Évaluation gratuite d’Azure][] ou [Crédits Azure MSDN][] pour créer un compte.
- **Serveur Azure SQL Server V12** : consultez [Créer un serveur logique de base de données SQL Azure avec le Portail Azure][] ou [Créer un serveur logique de base de données SQL Azure avec PowerShell][].
- **Nom de groupe de ressources** : utilisez le même groupe de ressources que votre serveur Azure SQL Server V12 ou consultez [groupes de ressources][] pour créer un groupe de ressources.
- **PowerShell version 1.0.3 or supérieure** : vous pouvez vérifier la version en exécutant **Get-Module -ListAvailable -Name Azure**. La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][]. Pour plus d’informations sur l’installation de la dernière version, consultez la page [Installation et configuration d’Azure PowerShell][].

> [AZURE.NOTE] La création d’un entrepôt SQL Data Warehouse peut entraîner un nouveau service facturable. Voir [Tarification de SQL Data Warehouse][] pour plus d’informations sur la tarification.

## Création d’une base de données SQL Data Warehouse
1. Ouvrez Windows PowerShell.
2. Exécutez cette applet de commande pour vous connecter à Azure Resource Manager.

	```Powershell
	Login-AzureRmAccount
	```
	
3. Sélectionnez l’abonnement que vous souhaitez utiliser pour votre session actuelle.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  Créez la base de données. Cet exemple crée la base de données « mynewsqldw », avec le niveau d’objectif de service « DW400 » sur le serveur nommé « sqldwserver1 », qui se trouve dans le groupe de ressources nommé « mywesteuroperesgp1 ».

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

Cette applet de commande requiert les paramètres suivants :

- **RequestedServiceObjectiveName** : quantité de [DWU][] que vous demandez. Valeurs prises en charge sont : DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, et DW2000.
- **DatabaseName** : nom de l’entrepôt SQL Data Warehouse que vous créez.
- **ServerName** : nom du serveur que vous utilisez pour la création (doit être V12).
- **ResourceGroupName** : groupe de ressources que vous utilisez. Pour trouver des groupes de ressources disponibles dans votre abonnement, utilisez Get-AzureResource.
- **Edition** : vous devez définir l'édition sur « DataWarehouse » pour créer un entrepôt SQL Data Warehouse.

Pour plus d’informations sur les options de paramètre, consultez [Créer une base de données (Azure SQL Data Warehouse)][]. Pour accéder aux informations de référence sur la commande, consultez [New-AzureRmSqlDatabase][].

## Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les données d’exemple][] ou découvrir comment [développer][], [charger][] ou [migrer][].

Si vous souhaitez en savoir plus sur la gestion de l’entrepôt SQL Data Warehouse par programme, consultez notre article sur l’utilisation des [applets de commande PowerShell et des API REST][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrer]: ./sql-data-warehouse-overview-migrate.md
[développer]: ./sql-data-warehouse-overview-develop.md
[charger]: ./sql-data-warehouse-load-with-bcp.md
[charger les données d’exemple]: ./sql-data-warehouse-get-started-load-sample-databases.md
[applets de commande PowerShell et des API REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Installation et configuration d’Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Créer un serveur logique de base de données SQL Azure avec le Portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Créer un serveur logique de base de données SQL Azure avec PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[groupes de ressources]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Créer une base de données (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Tarification de SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Évaluation gratuite d’Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédits Azure MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0706_2016-->