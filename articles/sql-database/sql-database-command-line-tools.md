<properties 
	pageTitle="Gérer la base de données SQL Azure avec PowerShell" 
	description="Gestion de la base de données SQL Azure avec PowerShell." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/09/2016" 
	ms.author="sstein"/>

# Gérer la base de données SQL Azure avec PowerShell


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

Cette rubrique fournit les commandes PowerShell permettant d’effectuer de nombreuses tâches de Base de données SQL Azure.

[AZURE.INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]


## Créer un groupe de ressources

Créez le groupe de ressources qui contient le serveur. Vous pouvez modifier la commande suivante pour utiliser un emplacement valide.

Pour obtenir la liste des emplacements valides de serveur de Base de données SQL Azure, exécutez l’applet de commande suivante :

	$AzureSQLLocations = (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Sql'}).Locations

Si vous disposez déjà d’un groupe de ressources, vous pouvez créer directement un serveur ou modifier et exécuter la commande suivante pour créer un nouveau groupe de ressources :

	New-AzureRmResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Créer un serveur 

Pour créer un serveur V12, utilisez l’applet de commande [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Remplacez server12 par le nom de votre serveur. Il doit être unique pour les serveurs SQL Azure. Vous recevez donc une erreur si le nom du serveur est déjà utilisé. Il est également à noter que l'exécution de cette commande peut prendre plusieurs minutes. Les détails du serveur et l'invite PowerShell apparaîtront une fois le serveur créé. Vous pouvez modifier la commande pour utiliser un emplacement valide.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Lorsque vous exécutez cette commande, une fenêtre s'ouvre dans laquelle vous devez entrer un **Nom d'utilisateur** et un **Mot de passe**. Il ne s'agit pas de vos informations d'identification Azure. Entrez le nom d'utilisateur et le mot de passe qui seront les informations d'identification d'administrateur que vous souhaitez créer pour le nouveau serveur.

## Créer une règle de pare-feu du serveur

Pour créer une règle de pare-feu et accéder au serveur, utilisez la commande [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Exécutez la commande suivante en remplaçant les adresses IP de début et de fin par des valeurs valides pour votre client.

Si votre serveur doit autoriser l'accès à d'autres services Azure, ajoutez le commutateur **-AllowAllAzureIPs** qui insère une règle de pare-feu spéciale et autorise le trafic Azure complet à accéder au serveur.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Pour en savoir plus, consultez [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Créer une base de données SQL

Pour créer une base de données, utilisez la commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). Vous avez besoin d’un serveur pour créer une base de données. L’exemple suivant crée une base de données SQL nommée TestDB12. La base de données est créée dans une base de données Standard S1.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Modifier le niveau de performances d’une base de données SQL

Vous pouvez faire monter ou descendre en puissance votre base de données à l’aide de la commande [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). L’exemple suivant met à l’échelle une base de données SQL nommée TestDB12 à partir de son niveau de performance actuelle à un niveau S3 Standard.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Supprimer une base de données SQL

Vous pouvez supprimer une base de données SQL à l’aide de la commande [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). L’exemple suivant supprime une base de données SQL nommée TestDB12.

	Remove-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Supprimer un serveur

Vous pouvez également supprimer un serveur à l’aide de la commande [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx). L’exemple suivant supprime un serveur nommé server12.

	Remove-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Si vous comptez recréer ces ressources SQL Azure ou des ressources similaires, vous pouvez :

- Enregistrer ceci en tant que fichier de script PowerShell (*.ps1)
- Enregistrer ceci en tant runbook d’automation Azure dans la section Automation du portail Azure Classic 

## Étapes suivantes

Combiner des commandes et l’automatisation. Par exemple, remplacez tous les éléments entre guillemets, y compris les caractères < and > par vos valeurs pour créer un serveur, une règle de pare-feu et une base de données :


    New-AzureRmResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRmSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRmSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Informations connexes

- [Applets de commande de la base de données SQL Azure.](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_0615_2016-->