<properties
	pageTitle="Configuration des paramètres du pare-feu (Base de données SQL Azure)"
	description="configurer le pare-feu pour les bases de données SQL Azure"
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
	ms.topic="get-started-article" 
	ms.date="06/22/2015"
	ms.author="rickbyh"/>


# Configuration des paramètres du pare-feu (Base de données SQL Azure)

 Base de données SQL Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et aux bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour le master ou une base de données utilisateur sur votre serveur de Base de données SQL Azure pour autoriser l’accès à la base de données de façon sélective.

**Important** pour autoriser les applications d’Azure à se connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, consultez [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Règles de pare-feu au niveau du serveur

Les règles de pare-feu au niveau du serveur peuvent être créées et gérées via le portail de gestion Microsoft Azure, Transact-SQL, Azure PowerShell ou API REST.

### Gérer les règles de pare-feu au niveau du serveur via le nouveau portail Azure
1. Visitez le portail Azure sur https://portal.azure.com et connectez-vous à votre compte Azure administrateur ou collaborateur.
2. Sur la bannière de gauche, cliquez sur PARCOURIR TOUT, faites défiler vers le bas, puis cliquez sur serveurs SQL.
3. Cliquez sur le serveur pour lequel vous souhaitez configurer des règles de pare-feu dans la liste SQL Server affichée.

	![pare-feu][1]

4. Dans le panneau de serveur, cliquez sur Paramètres, en haut du panneau, puis cliquez sur Pare-feu pour ouvrir le panneau Paramètres de pare-feu pour le serveur.
5. Ajouter ou modifier une règle de pare-feu.

	* Pour ajouter l’adresse IP de l’ordinateur actuel, cliquez sur **Ajouter IP du Client** en haut du panneau.
	* Pour ajouter des adresses IP supplémentaires, remplissez les champs **NOM DE LA RÈGLE**, adresse **IP DE DÉBUT** et adresse **IP DE FIN**.
	* Pour modifier une règle existante, cliquez et modifiez les champs dans la règle.
	* Pour supprimer une règle existante, cliquez sur la règle et cliquez sur les points de suspension (...) à la fin de la ligne, puis cliquez sur **Supprimer**.
6. Cliquez sur Enregistrer en haut du panneau Paramètres de pare-feu pour enregistrer les modifications. ![panneau de pare-feu][2] 

## Gérer les règles de pare-feu au niveau du serveur via le portail de gestion 

1. Dans le portail de gestion, cliquez sur **Base de données SQL**. Toutes les bases de données et leurs serveurs correspondants sont répertoriés ici.
1. Cliquez sur **Serveurs** en haut de la page.
2. Cliquez sur la flèche en regard du serveur pour lequel vous souhaitez gérer les règles de pare-feu.
3. Cliquez sur **Configurer** en haut de la page.

	*  Pour ajouter l’ordinateur actuel, cliquez sur Ajouter aux adresses IP autorisées.
	*  Pour ajouter des adresses IP supplémentaires, remplissez les champs Nom de la règle, Adresse IP de début et Adresse IP de fin.
	*  Pour modifier une règle existante, cliquez sur les champs dans la règle et modifiez-les.
	*  Pour supprimer une règle existante, placez le pointeur sur la règle jusqu’à ce que le X s’affiche à la fin de la ligne. Cliquez sur X pour supprimer la règle.
8. Pour enregistrer les modifications, cliquez sur **Enregistrer** en bas de la page.

## Gérer les règles de pare-feu au niveau du serveur via Transact-SQL
1. Lancez une fenêtre de requête par le biais du portail de gestion ou SQL Server Management Studio.
2. Assurez-vous d’être connecté à la base de données master.
3. Les règles de pare-feu au niveau du serveur peuvent être créées, mises à jour ou supprimés dans la fenêtre de requête.
4. Pour créer ou mettre à jour les règles de pare-feu au niveau du serveur, exécutez la procédure stockée sp_set_firewall_rule. L’exemple suivant active une plage d’adresses IP sur le serveur de Contoso.

		EXEC sp_set_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Pour supprimer une règle de pare-feu au niveau du serveur, exécutez la procédure stockée sp_delete_firewall_rule. L’exemple suivant supprime la règle nommée ContosoFirewallRule.
 
		EXEC sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
## Gérer les règles de pare-feu au niveau du serveur via Azure PowerShell
1. Lancez Azure PowerShell.
2. Les règles de pare-feu au niveau du serveur peuvent être créées, mises à jour et supprimées à l’aide d’Azure PowerShell. 

	Pour créer une nouvelle règle de pare-feu au niveau du serveur, exécutez l’applet de commande New-AzureSqlDatabaseServerFirewallRule. L’exemple suivant active une plage d’adresses IP sur le serveur de Contoso.
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	Pour modifier une règle de pare-feu au niveau du serveur, exécutez l’applet de commande Set-AzureSqlDatabaseServerFirewallRule. L’exemple suivant modifie la plage d’adresses IP acceptables pour la règle nommée ContosoFirewallRule.
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	Pour supprimer une règle de pare-feu existante au niveau du serveur, exécutez l’applet de commande Remove-AzureSqlDatabaseServerFirewallRule. L’exemple suivant supprime la règle nommée ContosoFirewallRule.

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso
 
## Gérer les règles de pare-feu au niveau du serveur via API REST
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

	Pour créer une nouvelle règle de pare-feu au niveau de la base de données ou pour mettre à jour une règle existante, exécutez la procédure stockée sp_set_database_firewall_rule. L’exemple suivant crée une nouvelle règle de pare-feu nommée ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Pour supprimer une règle de pare-feu au niveau de la base de données, exécutez la procédure stockée sp_delete_database_firewall_rule. L’exemple suivant supprime la règle nommée ContosoFirewallRule.
 
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

Pour consulter un didacticiel sur la création d’une base de données, reportez-vous à la rubrique [Créer votre première base de données SQL Azure](sql-database-get-started.md). Pour obtenir de l’aide pour vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Instructions de connexion par programme à une Base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee336282.aspx). Pour comprendre comment naviguer dans les bases de données, consultez [Gérer les bases de données et les connexions dans Base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=July15_HO4-->