<properties
	pageTitle="Configurer un pare-feu sur une base de données Azure SQL | Microsoft Azure"
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
	ms.date="02/04/2016"
	ms.author="rickbyh"/>


# Configurer un pare-feu sur une base de données Azure SQL à l’aide de l’API REST


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Base de données SQL Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et aux bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour le master ou une base de données utilisateur sur votre serveur de Base de données SQL Azure pour autoriser l’accès à la base de données de façon sélective.

> [AZURE.IMPORTANT] Pour autoriser des applications d’Azure à se connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de la base de données SQL Azure](sql-database-firewall-configure.md). Si vous effectuez des connexions dans la limite du cloud Azure, vous devez peut-être ouvrir des ports TCP supplémentaires. Pour plus d’informations, voir la section **V12 de SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).


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


## Gérer les règles de pare-feu à l’aide de l’API REST de gestion de Service

* [Créer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Supprimer une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Obtenir une règle de pare-feu](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [Répertorier les règles de pare-feu](https://msdn.microsoft.com/library/azure/dn505715.aspx)
 
## Étapes suivantes

Pour consulter un didacticiel sur la création d’une base de données, voir [Création de votre première base de données SQL Azure](sql-database-get-started.md). Pour obtenir de l’aide pour vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Instructions de connexion par programmation à Azure SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx). Pour comprendre comment naviguer dans les bases de données, consultez [Gérer les bases de données et les connexions dans Base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0420_2016-->