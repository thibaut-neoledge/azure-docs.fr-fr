<properties
	pageTitle="Règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL | Microsoft Azure"
	description="Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="08/30/2016"
	ms.author="rickbyh"/>


# Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-firewall-configure.md)
- [Portail Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Base de données SQL Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et aux bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour le master ou une base de données utilisateur sur votre serveur de Base de données SQL Azure pour autoriser l’accès à la base de données de façon sélective.

> [AZURE.IMPORTANT] Pour autoriser des applications d’Azure à se connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de la base de données SQL Azure](sql-database-firewall-configure.md). Si vous effectuez des connexions dans la limite du cloud Azure, vous devrez peut-être ouvrir des ports TCP supplémentaires. Pour plus d’informations, voir la section **V12 de SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Règles de pare-feu au niveau du serveur

Seuls la connexion du principal au niveau du serveur et l’administrateur Azure Active Directory peuvent créer une règle de pare-feu de niveau serveur à l’aide de Transact-SQL.

1. Ouvrez une fenêtre de requête et connectez-vous à la base de données master virtuelle à l’aide de SQL Server Management Studio.
2. Les règles de pare-feu au niveau du serveur peuvent être sélectionnées, créées, mises à jour ou supprimées dans la fenêtre de requête.
3. Pour créer ou mettre à jour les règles de pare-feu au niveau du serveur, exécutez la procédure stockée `sp_set_firewall_rule`. L’exemple suivant active une plage d’adresses IP sur le serveur Contoso.<br/>Commencez par regarder quelles règles existent déjà.

		SELECT * FROM sys.firewall_rules ORDER BY name;

	Ensuite, ajoutez une règle de pare-feu.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Pour supprimer une règle de pare-feu au niveau du serveur, exécutez la procédure stockée sp\_delete\_firewall\_rule. L’exemple suivant supprime la règle nommée ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Pour plus d’informations sur ces procédures stockées, consultez [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) et [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## Règles de pare-feu au niveau de la base de données

Seul un utilisateur de base de données disposant de l’autorisation **CONTRÔLE** sur la base de données (par exemple, le propriétaire de la base de données) peut créer une règle de pare-feu au niveau de la base de données.

1. Après avoir créé un pare-feu au niveau du serveur pour votre adresse IP, lancez une fenêtre de requête par le biais du portail Classic ou de SQL Server Management Studio.
2. Connectez-vous à la base de données pour laquelle vous souhaitez créer une règle de pare-feu de niveau base de données.

	Pour créer une règle de pare-feu au niveau de la base de données ou pour mettre à jour une règle existante, exécutez la procédure stockée`sp_set_database_firewall_rule`. L’exemple suivant crée une nouvelle règle de pare-feu nommée ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
		    @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Pour supprimer une règle de pare-feu existante au niveau de la base de données ou pour mettre à jour une règle existante, exécutez la procédure stockée`sp_delete_database_firewall_rule`. L’exemple suivant supprime la règle ContosoFirewallRule. ’EXEC sp\_delete\_database\_firewall\_rule @name = N'ContosoFirewallRule'

Pour plus d’informations sur ces procédures stockées, consultez [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) et [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## Étapes suivantes

Pour lire des articles sur la création de règles de pare-feu de niveau du serveur à l’aide d’autres méthodes, consultez :

- [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
- [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour obtenir un didacticiel sur la création d’une base de données, consultez [Créer une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md). Pour obtenir de l’aide afin de vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour Base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Pour comprendre comment naviguer dans les bases de données, consultez [Gérer la sécurité d’accès et de connexion aux bases de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## Ressources supplémentaires

- [Sécurisation de votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0831_2016-->