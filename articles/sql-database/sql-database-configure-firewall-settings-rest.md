---
title: "API REST : Règles de pare-feu au niveau du serveur Azure SQL Database | Microsoft Docs"
description: "Découvrez comment configurer des règles de pare-feu au niveau du serveur pour les adresses IP qui accèdent aux bases de données SQL Azure à l’aide de l’API REST."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: fc874f3c-c623-4924-8cb7-32a8c31e666c
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: e164e1da7ec5f2da157900c86b3313fff1affed9
ms.lasthandoff: 02/17/2017


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de l’API REST

Base de données SQL Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et aux bases de données. Vous pouvez définir des paramètres de pare-feu au niveau du serveur et au niveau de la base de données pour le master ou une base de données utilisateur sur votre serveur de Base de données SQL Azure pour autoriser l’accès à la base de données de façon sélective.

> [!IMPORTANT]
> Pour autoriser des applications d’Azure à se connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de la base de données SQL Azure](sql-database-firewall-configure.md). Si vous effectuez des connexions dans la limite du cloud Azure, vous devrez peut-être ouvrir des ports TCP supplémentaires. Pour plus d’informations, voir la section **V12 de SQL Database : exécution externe ou exécution interne** de [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)
> 
> 

## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Gestion des règles de pare-feu au niveau du serveur via un API REST
1. La gestion des règles de pare-feu via l’API REST doit être authentifiée. Pour plus d’informations, consultez [Guide du développeur pour l’authentification avec l’API Azure Resource Manager](../azure-resource-manager/resource-manager-api-authentication.md).
2. Les règles au niveau du serveur peuvent être créées, mises à jour ou supprimées via API REST
   
    Pour créer ou mettre à jour une règle de pare-feu au niveau du serveur, exécutez la méthode PUT à l’aide des éléments suivants :
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    Corps de la requête
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    Pour supprimer une règle de pare-feu au niveau du serveur, exécutez la méthode DELETE à l’aide des éléments suivants :

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Gérer les règles de pare-feu à l’aide de l’API REST
* [Créer ou mettre à jour une règle de pare-feu](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Supprimer une règle de pare-feu](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Obtenir une règle de pare-feu](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Répertorier toutes les règles de pare-feu](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## <a name="next-steps"></a>Étapes suivantes
Pour lire un article sur l’utilisation de Transact-SQL afin de créer des règles de pare-feu au niveau du serveur et au niveau de la base de données, consultez [Configurer des règles de pare-feu au niveau du serveur et au niveau de la base de données sur une base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Pour lire des articles sur la création de règles de pare-feu de niveau du serveur à l’aide d’autres méthodes, consultez : 

* [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
* [Configurer des règles de pare-feu au niveau du serveur sur une base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)

Pour consulter un didacticiel sur la création d’une base de données, voir [Création de votre première base de données SQL Azure](sql-database-get-started.md).
Pour obtenir de l’aide afin de vous connecter à une base de données SQL Azure à partir d’applications open source ou tierces, consultez [Exemples de code de démarrage rapide client pour Base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour comprendre comment naviguer dans les bases de données, consultez [Gérer la sécurité d’accès et de connexion aux bases de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Sécurisation de votre base de données](sql-database-security-overview.md)
* [Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



