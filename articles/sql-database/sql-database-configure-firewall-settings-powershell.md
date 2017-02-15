---
title: "PowerShell : Configurer des règles de pare-feu d’Azure SQL Database | Microsoft Docs"
description: "Découvrez comment configurer des règles de pare-feu au niveau du serveur pour les adresses IP qui accèdent aux bases de données SQL Azure à l’aide de PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 30dcea72-61c1-48b6-8e1d-b1db2eb61567
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: d80bd1fbb5cdb0492e521a4d600f657fac0e3325


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-firewall-configure.md)
> * [Portail Azure](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [API REST](sql-database-configure-firewall-settings-rest.md)
> 
> 

La base de données SQL Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et aux bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour la base de données master ou une base de données utilisateur sur votre serveur de Base de données SQL pour autoriser l’accès à la base de données de façon sélective.

> [!IMPORTANT]
> Pour autoriser des applications d’Azure à se connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de la base de données SQL Azure](sql-database-firewall-configure.md). Si vous effectuez des connexions dans la limite du cloud Azure, vous devrez peut-être ouvrir des ports TCP supplémentaires. Pour plus d’informations, voir la section « V12 de SQL Database : exécution externe ou exécution interne » de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Créer des règles de pare-feu au niveau du serveur
Les règles de pare-feu au niveau du serveur peuvent être créées, mises à jour et supprimées à l’aide d’Azure PowerShell.

Pour créer une nouvelle règle de pare-feu au niveau du serveur, exécutez l’applet de commande [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). L’exemple suivant active une plage d’adresses IP sur le serveur Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

Pour modifier une règle de pare-feu au niveau du serveur, exécutez l’applet de commande [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx). L’exemple suivant modifie la plage d’adresses IP acceptables pour la règle nommée ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -StartIPAddress 192.168.1.4 -EndIPAddress 192.168.1.10 -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'

Pour supprimer une règle de pare-feu au niveau du serveur, exécutez l’applet de commande [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx). L’exemple suivant supprime la règle nommée ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Gérer les règles de pare-feu à l’aide de PowerShell
Vous pouvez également utiliser PowerShell pour gérer les règles de pare-feu. Pour plus d’informations, consultez les rubriques suivantes :

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586\(v=azure.300\).aspx)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de Transact-SQL afin de créer des règles de pare-feu au niveau du serveur et au niveau de la base de données, consultez [Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md).

Pour plus d’informations sur la création de règles de pare-feu de niveau du serveur à l’aide d’autres méthodes, consultez :

* [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
* [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour obtenir un didacticiel sur la création d’une base de données, consultez [Créer une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md).
Pour obtenir de l’aide afin de vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour comprendre comment naviguer dans les bases de données, consultez [Gérer la sécurité d’accès et de connexion aux bases de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Sécurisation de votre base de données](sql-database-security-overview.md)
* [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



<!--HONumber=Jan17_HO1-->


