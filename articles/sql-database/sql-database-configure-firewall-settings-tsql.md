---
title: "Règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL | Microsoft Docs"
description: "Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: e5b5751facb68ae4a62e3071fe4dfefc02434a9f
ms.openlocfilehash: a49bc9cd8fb50dcc753fbb04bf7f2d96cf79f3ab


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-firewall-configure.md)
> * [Portail Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

Base de données SQL Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et aux bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour le master ou une base de données utilisateur sur votre serveur de Base de données SQL Azure pour autoriser l’accès à la base de données de façon sélective.

> [!IMPORTANT]
> Pour autoriser des applications d’Azure à se connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de la base de données SQL Azure](sql-database-firewall-configure.md). Si vous effectuez des connexions dans la limite du cloud Azure, vous devrez peut-être ouvrir des ports TCP supplémentaires. Pour plus d’informations, voir la section **V12 de SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="server-level-firewall-rules"></a>Règles de pare-feu au niveau du serveur
Seuls la connexion du principal au niveau du serveur et l’administrateur Azure Active Directory peuvent créer une règle de pare-feu de niveau serveur à l’aide de Transact-SQL.

1. Ouvrez une fenêtre de requête et connectez-vous à la base de données master virtuelle à l’aide de SQL Server Management Studio.
2. Les règles de pare-feu au niveau du serveur peuvent être sélectionnées, créées, mises à jour ou supprimées dans la fenêtre de requête.
3. Pour créer ou mettre à jour les règles de pare-feu au niveau du serveur, exécutez la procédure stockée `sp_set_firewall_rule` . L’exemple suivant active une plage d’adresses IP sur le serveur Contoso.<br/>Commencez par regarder quelles règles existent déjà.
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    Ensuite, ajoutez une règle de pare-feu.
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    Pour supprimer une règle de pare-feu au niveau du serveur, exécutez la procédure stockée sp_delete_firewall_rule. L’exemple suivant supprime la règle nommée ContosoFirewallRule.
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   Pour plus d’informations sur ces procédures stockées, consultez [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) et [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Règles de pare-feu au niveau de la base de données
Seul un utilisateur de base de données disposant de l’autorisation **CONTRÔLE** sur la base de données (par exemple, le propriétaire de la base de données) peut créer une règle de pare-feu au niveau de la base de données.

1. Après avoir créé un pare-feu au niveau du serveur pour votre adresse IP, lancez une fenêtre de requête par le biais du portail Classic ou de SQL Server Management Studio.
2. Connectez-vous à la base de données pour laquelle vous souhaitez créer une règle de pare-feu de niveau base de données.
   
    Pour créer une règle de pare-feu au niveau de la base de données ou pour mettre à jour une règle existante, exécutez la procédure stockée `sp_set_database_firewall_rule` . L’exemple suivant crée une nouvelle règle de pare-feu nommée ContosoFirewallRule.
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    Pour supprimer une règle de pare-feu existante au niveau de la base de données ou pour mettre à jour une règle existante, exécutez la procédure stockée `sp_delete_database_firewall_rule` . L’exemple suivant supprime la règle nommée ContosoFirewallRule.
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Pour plus d’informations sur ces procédures stockées, consultez [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) et [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## <a name="next-steps"></a>Étapes suivantes
Pour lire des articles sur la création de règles de pare-feu de niveau du serveur à l’aide d’autres méthodes, consultez : 

* [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
* [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour obtenir un didacticiel sur la création d’une base de données, consultez [Créer une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md).
Pour obtenir de l’aide afin de vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour Base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour comprendre comment naviguer dans les bases de données, consultez [Gérer la sécurité d’accès et de connexion aux bases de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Sécurisation de votre base de données](sql-database-security-overview.md)
* [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)




<!--HONumber=Dec16_HO4-->


