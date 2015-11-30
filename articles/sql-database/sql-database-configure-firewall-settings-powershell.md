<properties
	pageTitle="Configuration des paramètres du pare-feu | Microsoft Azure"
	description="Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="11/13/2015"
	ms.author="rickbyh"/>


# Configuration des paramètres du pare-feu sur une base de données SQL à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Base de données SQL Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et aux bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour le master ou une base de données utilisateur sur votre serveur de Base de données SQL Azure pour autoriser l’accès à la base de données de façon sélective.

> [AZURE.IMPORTANT]Pour autoriser des applications d’Azure à se connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de la base de données SQL Azure](sql-database-firewall-configure.md). Si vous effectuez des connexions dans la limite du cloud Azure, vous devez peut-être ouvrir des ports TCP supplémentaires. Pour plus d’informations, voir la section **V12 de SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Gestion des règles de pare-feu au niveau du serveur via Azure PowerShell
1. Lancez Azure PowerShell.
2. Les règles de pare-feu au niveau du serveur peuvent être créées, mises à jour et supprimées à l’aide d’Azure PowerShell. 

	Pour créer une nouvelle règle de pare-feu au niveau du serveur, exécutez l’applet de commande New-AzureSqlDatabaseServerFirewallRule. L’exemple suivant active une plage d’adresses IP sur le serveur de Contoso.
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	Pour modifier une règle de pare-feu au niveau du serveur, exécutez l’applet de commande Set-AzureSqlDatabaseServerFirewallRule. L’exemple suivant modifie la plage d’adresses IP acceptables pour la règle nommée ContosoFirewallRule.
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	Pour supprimer une règle de pare-feu existante au niveau du serveur, exécutez l’applet de commande Remove-AzureSqlDatabaseServerFirewallRule. L’exemple suivant supprime la règle nommée ContosoFirewallRule.

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso


## Gérer les règles de pare-feu à l’aide de PowerShell

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## Étapes suivantes

Pour consulter un didacticiel sur la création d’une base de données, voir [Création de votre première base de données SQL Azure](sql-database-get-started.md). Pour obtenir de l’aide pour vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, voir [Instructions de connexion par programmation à une base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee336282.aspx). Pour comprendre comment naviguer dans les bases de données, voir [Gestion des bases de données et des connexions dans la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=Nov15_HO4-->