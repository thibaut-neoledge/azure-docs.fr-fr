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
   ms.date="03/26/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Création de SQL Data Warehouse à l'aide de Powershell

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Acquérir et exécuter les applets de commande Azure PowerShell

> [AZURE.NOTE]  Pour utiliser Microsoft Azure PowerShell avec SQL Data Warehouse, vous devez télécharger et installer la dernière version d’Azure PowerShell avec les applets de commande ARM. Vous pouvez exécuter `Get-Module -ListAvailable -Name Azure` pour vérifier votre version. Cet article est basé sur Microsoft Azure PowerShell 1.0.3 ou version ultérieure.

Si vous ne disposez pas encore de la configuration PowerShell, vous devez la télécharger et la configurer.

1. Pour télécharger le module Azure PowerShell, exécutez [Microsoft Web Platform Installer](http://aka.ms/webpi-azps). Pour plus d’informations sur ce programme d’installation, consultez [Comment installer et configurer Azure PowerShell][].
2. Pour exécuter le module, entrez **Windows PowerShell** dans la fenêtre de démarrage.
3. Exécutez cette applet de commande pour vous connecter à Azure Resource Manager.

	```Powershell
	Login-AzureRmAccount
	```

4. Sélectionnez l’abonnement que vous souhaitez utiliser pour votre session actuelle.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

## Création d’une base de données SQL Data Warehouse
Pour déployer une base de données SQL Data Warehouse, utilisez l’applet de commande New-AzureRmSQLDatabase. Avant d’exécuter la commande, vérifiez que les conditions préalables suivantes sont remplies.

### Composants requis

- Un serveur Azure SQL Server V12 pour héberger la base de données
- Connaître le nom du groupe de ressources pour le serveur SQL Server

### Commande de déploiement

Cette commande déploie une nouvelle base de données dans SQL Data Warehouse.

```Powershell
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

Cet exemple déploie la nouvelle base de données « mynewsqldw1 », avec le niveau d’objectif de service « DW400 » sur le serveur nommé « sqldwserver1 », qui se trouve dans le groupe de ressources nommé « mywesteuroperesgp1 ».

```Powershell
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
```

Les paramètres nécessaires pour l'applet de commande sont les suivants :

 + **RequestedServiceObjectiveName** : quantité de DWU que vous demandez, sous la forme « DWXXX ». Les valeurs actuellement prises en charge sont : 100, 200, 300, 400, 500, 600, 1 000, 1 200, 1 500, 2 000.
 + **DatabaseName** : nom de l’entrepôt SQL Data Warehouse que vous créez.
 + **ServerName** : nom du serveur que vous utilisez pour la création (doit être V12).
 + **ResourceGroupName** : groupe de ressources que vous utilisez. Pour trouver des groupes de ressources disponibles dans votre abonnement, utilisez Get-AzureResource.
 + **Edition** : vous devez définir l'édition sur « DataWarehouse » pour créer un entrepôt SQL Data Warehouse.

Pour accéder aux informations de référence sur la commande, consultez [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx).

Pour connaître les options de paramètre, consultez [Créer une base de données (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx).

## Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les données d’exemple][] ou découvrir comment [développer][], [charger] ou [migrer][] les données.

Si vous souhaitez en savoir plus sur la gestion de l’entrepôt SQL Data Warehouse par programme, consultez notre documentation [Powershell][] ou [API REST][].



<!--Image references-->

<!--Article references-->
[migrer]: ./sql-data-warehouse-overview-migrate.md
[développer]: ./sql-data-warehouse-overview-develop/.md
[charger les données d’exemple]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[API REST]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
[Comment installer et configurer Azure PowerShell]: ./powershell-install-configure.md

<!---HONumber=AcomDC_0330_2016-->