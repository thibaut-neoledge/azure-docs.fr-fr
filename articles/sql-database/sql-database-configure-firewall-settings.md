<properties
	pageTitle="Configurer une règle de pare-feu au niveau du serveur sur une base de données SQL | Microsoft Azure"
	description="Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent au serveur SQL Azure."
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
	ms.date="06/10/2016"
	ms.author="rickbyh;carlrab"/>


# Configurer une règle de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-firewall-configure.md)
- [Portail Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)

Azure SQL Server utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour la base de données MASTER ou une base de données utilisateur dans votre serveur logique Azure SQL Server pour autoriser l’accès à la base de données de façon sélective. Cette rubrique évoque les règles de pare-feu au niveau du serveur.

> [AZURE.IMPORTANT] Pour autoriser des applications d’Azure à se connecter à Azure SQL Server, les connexions Azure doivent être activées. Pour comprendre comment les règles de pare-feu fonctionnent, consultez [Comment configurer un pare-feu de serveur SQL Azure - Présentation](sql-database-firewall-configure.md). Si vous effectuez des connexions dans la limite du cloud Azure, vous devez peut-être ouvrir des ports TCP supplémentaires. Pour plus d’informations, voir la section **V12 de SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).

**Recommandation ** Utilisez des règles de pare-feu pour les administrateurs au niveau du serveur quand plusieurs bases de données ont les mêmes exigences d’accès et que vous ne souhaitez les configurer une à une. Microsoft recommande d’utiliser, dans la mesure du possible, des règles de pare-feu au niveau de la base de données pour améliorer la sécurité et renforcer la portabilité de la base de données.

[AZURE.INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Gérer des règles de pare-feu au niveau du serveur existantes via le portail Azure

Répétez les étapes pour gérer les règles de pare-feu au niveau du serveur.

- Pour ajouter l’ordinateur actuel, cliquez sur Ajouter une adresse IP cliente.
- Pour ajouter des adresses IP supplémentaires, remplissez les champs Nom de la règle, Adresse IP de début et Adresse IP de fin.
- Pour modifier une règle existante, cliquez sur les champs dans la règle et modifiez-les.
- Pour supprimer une règle existante, placez le pointeur sur la règle jusqu’à ce que le X s’affiche à la fin de la ligne. Cliquez sur X pour supprimer la règle.

Cliquez sur **Enregistrer** pour enregistrer les modifications.

## Étapes suivantes

Pour lire un article sur l’utilisation de Transact-SQL afin de créer des règles de pare-feu au niveau du serveur et au niveau de la base de données, consultez [Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md).

Pour lire des articles sur la création de règles de pare-feu de niveau du serveur à l’aide d’autres méthodes, consultez :

- [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour obtenir un didacticiel sur la création d’une base de données, consultez [Créer une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md). Pour obtenir de l’aide afin de vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx). Pour comprendre comment naviguer dans les bases de données, consultez [Gérer la sécurité d’accès et de connexion aux bases de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## Ressources supplémentaires

- [Sécurisation de votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0622_2016-->