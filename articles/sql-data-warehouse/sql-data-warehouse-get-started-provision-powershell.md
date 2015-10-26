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
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/08/2015"
   ms.author="lodipalm"/>

# Création de SQL Data Warehouse à l'aide de Powershell

> [AZURE.SELECTOR]
- [Azure preview portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

> [AZURE.NOTE]Pour utiliser Microsoft Azure Powershell avec SQL Data Warehouse, vous devez disposer de la version 0.9.4 ou supérieure. Vous pouvez vérifier la version en exécutant (Get-Module Azure).Version dans Powershell.

## Acquérir et exécuter les applets de commande Azure PowerShell
Si Powershell n'est pas encore configuré, vous pouvez :

1. Pour télécharger le module Azure PowerShell, exécutez [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Pour exécuter le module, tapez **Microsoft Azure PowerShell** dans la fenêtre de démarrage.
3. Si vous n’avez pas encore ajouté votre compte à la machine, exécutez l’applet de commande suivante. (Pour plus d’informations, consultez la rubrique [Comment installer et configurer Azure PowerShell][]) :

            Add-AzureAccount

4. Vous devrez également exécuter PowerShell en mode ARM. Vous pouvez configurer ce mode en exécutant la commande suivante :

            switch-azuremode AzureResourceManager

## Création de SQL Data Warehouse
Après vous être assuré que Powershell est correctement configuré sur votre compte, vous pouvez exécuter ce qui suit pour déployer un nouveau SQL Data Warehouse :

        New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"

Les paramètres nécessaires pour l'applet de commande sont les suivants :

 + **RequestedServiceObjectiveName** : quantité de DWU que vous demandez, sous la forme « DWXXX ». Les valeurs actuellement prises en charge sont : 100, 200, 300, 400, 500, 600, 1 000, 1 200, 1 500, 2 000.
 + **DatabaseName** : nom de l’entrepôt SQL Data Warehouse que vous créez.
 + **ServerName** : nom du serveur que vous utilisez pour la création (doit être V12).
 + **ResourceGroupName** : groupe de ressources que vous utilisez. Pour trouver des groupes de ressources disponibles dans votre abonnement, utilisez Get-AzureResource.
 + **Edition** : vous devez définir l’édition sur « DataWarehouse » pour créer un entrepôt SQL Data Warehouse. 

## Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les données d’exemple][] ou découvrir comment [développer][], [charger][] ou [migrer][].

Si vous souhaitez en savoir plus sur la gestion de l’entrepôt SQL Data Warehouse par programme, consultez notre documentation [Powershell][] ou [API REST][].



<!--Image references-->

<!--Article references-->
[migrer]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-migrate/
[développer]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-develop/
[charger]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-load/
[charger les données d’exemple]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Powershell]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-reference-powershell-cmdlets/
[API REST]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/fr-FR/library/azure/dn546722.aspx
[firewall rules]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-database-configure-firewall-settings/
[Comment installer et configurer Azure PowerShell]: powershell-install-configure.md

<!---HONumber=Oct15_HO3-->