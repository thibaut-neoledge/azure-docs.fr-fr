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
   ms.date="01/11/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Création de SQL Data Warehouse à l'aide de Powershell

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

> [AZURE.NOTE]Pour utiliser Microsoft Azure Powershell avec SQL Data Warehouse, vous devez disposer de la version 1.0 ou supérieure. Vous pouvez vérifier la version en exécutant (Get-Module Azure).Version dans Powershell.

## Acquérir et exécuter les applets de commande Azure PowerShell
Si vous ne disposez pas encore de la configuration PowerShell, vous devez la télécharger et la configurer.

1. Pour télécharger le module Azure PowerShell, exécutez [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Pour exécuter le module, tapez **Microsoft Azure PowerShell** dans la fenêtre de démarrage.
3. Si vous n’avez pas encore ajouté votre compte à la machine, exécutez l’applet de commande suivante. (Pour plus d’informations, consultez la rubrique [Comment installer et configurer Azure PowerShell][]) :

```
Add-AzureAccount
```

4. Sélectionnez l’abonnement que vous souhaitez utiliser. Cet exemple permet d’obtenir la liste des noms d’abonnement. Puis, il définit le nom d’abonnement sur « MySubscription ». 

```
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```
   
## Création de SQL Data Warehouse
Une fois que PowerShell est configuré pour votre compte, vous pouvez exécuter la commande suivante pour déployer une nouvelle base de données SQL Data Warehouse.

```
New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

Les paramètres nécessaires pour l'applet de commande sont les suivants :

 + **RequestedServiceObjectiveName** : quantité de DWU que vous demandez, sous la forme « DWXXX ». Les valeurs actuellement prises en charge sont : 100, 200, 300, 400, 500, 600, 1 000, 1 200, 1 500, 2 000.
 + **DatabaseName** : nom de l’entrepôt SQL Data Warehouse que vous créez.
 + **ServerName** : nom du serveur que vous utilisez pour la création (doit être V12).
 + **ResourceGroupName** : groupe de ressources que vous utilisez. Pour trouver des groupes de ressources disponibles dans votre abonnement, utilisez Get-AzureResource.
 + **Edition** : vous devez définir l'édition sur « DataWarehouse » pour créer un entrepôt SQL Data Warehouse. 

Pour accéder à la référence des commandes, consultez [New-AzureSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx)

Pour les options de paramètre, consultez [Créer une base de données (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx).

## Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les données d’exemple][] ou découvrir comment [développer][], [charger][] ou [migrer][] les données.

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

<!---HONumber=AcomDC_0114_2016-->