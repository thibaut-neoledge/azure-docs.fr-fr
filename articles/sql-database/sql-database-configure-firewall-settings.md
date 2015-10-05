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
	ms.date="09/04/2015"
	ms.author="rickbyh"/>


# Configuration des paramètres du pare-feu sur une base de données SQL


Base de données SQL Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et aux bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour le master ou une base de données utilisateur sur votre serveur de Base de données SQL Azure pour autoriser l’accès à la base de données de façon sélective.

> [AZURE.IMPORTANT]Pour autoriser des applications d’Azure à se connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de la base de données SQL Azure](sql-database-firewall-configure.md). Si vous effectuez des connexions dans la limite du cloud Azure, vous devez peut-être ouvrir des ports TCP supplémentaires. Pour plus d’informations, voir la section **V12 de SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Règles de pare-feu au niveau du serveur

Les règles de pare-feu au niveau du serveur peuvent être créées et gérées via le portail de gestion Microsoft Azure, Transact-SQL, Azure PowerShell ou API REST.

### Gestion des règles de pare-feu au niveau du serveur via le nouveau portail Azure


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## Gestion des règles de pare-feu au niveau du serveur via le portail de gestion 

1. Dans le portail de gestion, cliquez sur **Base de données SQL**. Toutes les bases de données et leurs serveurs correspondants sont répertoriés ici.
2. Cliquez sur **Serveurs** en haut de la page.
3. Cliquez sur la flèche en regard du serveur pour lequel vous souhaitez gérer les règles de pare-feu.
4. Cliquez sur **Configurer** en haut de la page.

	*  Pour ajouter l’ordinateur actuel, cliquez sur Ajouter aux adresses IP autorisées.
	*  Pour ajouter des adresses IP supplémentaires, remplissez les champs Nom de la règle, Adresse IP de début et Adresse IP de fin.
	*  Pour modifier une règle existante, cliquez sur les champs dans la règle et modifiez-les.
	*  Pour supprimer une règle existante, placez le pointeur sur la règle jusqu’à ce que le X s’affiche à la fin de la ligne. Cliquez sur X pour supprimer la règle.
5. Pour enregistrer les modifications, cliquez sur **Enregistrer** au bas de la page.

## Gestion des règles de pare-feu au niveau du serveur via Transact-SQL

1. Lancez une fenêtre de requête par le biais du portail de gestion ou SQL Server Management Studio.
2. Assurez-vous d’être connecté à la base de données master.
3. Les règles de pare-feu au niveau du serveur peuvent être sélectionnées, créées, mises à jour ou supprimées dans la fenêtre de requête.
4. Pour créer ou mettre à jour les règles de pare-feu au niveau du serveur, exécutez la procédure stockée sp\_set\_firewall\_rule. L’exemple suivant active une plage d’adresses IP sur le serveur Contoso.<br/> Commencez par regarder quelles règles existent déjà.

		SELECT * FROM sys.database_firewall_rules ORDER BY name;

	Ensuite, ajoutez une règle de pare-feu.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Pour supprimer une règle de pare-feu au niveau du serveur, exécutez la procédure stockée sp\_delete\_firewall\_rule. L’exemple suivant supprime la règle nommée ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
## Gestion des règles de pare-feu au niveau du serveur via Azure PowerShell
1. Lancez Azure PowerShell.
2. Les règles de pare-feu au niveau du serveur peuvent être créées, mises à jour et supprimées à l’aide d’Azure PowerShell. 

	Pour créer une nouvelle règle de pare-feu au niveau du serveur, exécutez l’applet de commande New-AzureSqlDatabaseServerFirewallRule. L’exemple suivant active une plage d’adresses IP sur le serveur de Contoso.
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	Pour modifier une règle de pare-feu au niveau du serveur, exécutez l’applet de commande Set-AzureSqlDatabaseServerFirewallRule. L’exemple suivant modifie la plage d’adresses IP acceptables pour la règle nommée ContosoFirewallRule.
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	Pour supprimer une règle de pare-feu existante au niveau du serveur, exécutez l’applet de commande Remove-AzureSqlDatabaseServerFirewallRule. L’exemple suivant supprime la règle nommée ContosoFirewallRule.

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso
 
## Gestion des règles de pare-feu au niveau du serveur via un API REST
1. La gestion des règles de pare-feu via l’API REST doit être authentifiée. Pour plus d’informations, consultez Authentification des requêtes de gestion de Service.
2. Les règles au niveau du serveur peuvent être créées, mises à jour ou supprimées via API REST

	Pour créer ou mettre à jour une règle de pare-feu au niveau du serveur, exécutez la méthode POST à l’aide des éléments suivants :
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	Corps de la requête

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	Pour supprimer une règle de pare-feu au niveau du serveur, exécutez la méthode DELETE à l’aide des éléments suivants :
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule
 
## Règles de pare-feu au niveau de la base de données

1. Après avoir créé un pare-feu au niveau du serveur pour votre adresse IP, lancez une fenêtre de requête par le biais du portail de gestion ou de SQL Server Management Studio.
2. Connectez-vous à la base de données pour laquelle vous souhaitez créer une règle de pare-feu de niveau base de données.

	Pour créer une nouvelle règle de pare-feu au niveau de la base de données ou pour mettre à jour une règle existante, exécutez la procédure stockée sp\_set\_database\_firewall\_rule. L’exemple suivant crée une nouvelle règle de pare-feu nommée ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Pour supprimer une règle de pare-feu au niveau de la base de données, exécutez la procédure stockée sp\_delete\_database\_firewall\_rule. L’exemple suivant supprime la règle nommée ContosoFirewallRule.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## Gérer les règles de pare-feu à l’aide de l’API REST de gestion de Service

* [Créer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Supprimer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Obtenir une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [Répertorier les règles de pare-feu](https://msdn.microsoft.com/library/azure/dn505715.aspx)

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

<!---HONumber=Sept15_HO4-->