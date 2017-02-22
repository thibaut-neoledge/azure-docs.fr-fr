---
title: "PowerShell : créer et gérer des serveurs Azure SQL Database | Microsoft Docs"
description: "Aide-mémoire sur la création et la gestion de serveurs Azure SQL Database avec PowerShell."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 144774c9106bf5a0e389c99075c822d1c5282692
ms.openlocfilehash: c22243db06c783bc4f5b311aa4d49ce12032ab02


---
 
# <a name="create-and-manage-azure-sql-database-servers-with-powershell"></a>Créer et gérer des serveurs Azure SQL Database avec PowerShell

Vous pouvez créer et gérer un serveur Azure SQL Database avec le [Portail Azure](https://portal.azure.com/), PowerShell, l’API REST ou C#. Cette rubrique porte sur l’utilisation de PowerShell. Pour le Portail Azure, consultez la page [Créer et gérer des serveurs à l’aide du Portail Azure](sql-database-manage-servers-portal.md). 

## <a name="create-an-azure-sql-database-server-using-powershell"></a>Création d’un serveur Azure SQL Database à l’aide de PowerShell

Pour créer un serveur SQL Database, utilisez l’applet de commande [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver). Remplacez *server1* par le nom de votre serveur. Le nom doit être unique pour tous les serveurs Azure SQL Database. Vous obtiendrez une erreur si le nom est déjà utilisé pour un autre serveur. Cette commande peut prendre plusieurs minutes. Le groupe de ressources doit déjà exister dans votre abonnement.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

> [!TIP]
> Pour voir un exemple de script, consultez la page [Créer une base de données SQL à l’aide d’un script PowerShell](sql-database-get-started-powershell.md).
>

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble des outils de gestion, consultez [Vue d’ensemble des outils de gestion](sql-database-manage-overview.md)
* Pour découvrir comment effectuer des tâches de gestion à l’aide du portail Azure, consultez [Gérer des bases de données SQL Azure au moyen du portail Azure](sql-database-manage-portal.md)
* Pour découvrir comment effectuer des tâches de gestion à l’aide de PowerShell, consultez [Gérer des bases de données SQL Azure au moyen de PowerShell](sql-database-manage-powershell.md)
* Pour découvrir comment effectuer des tâches supplémentaires à l’aide de SQL Server Management Studio, consultez [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Pour plus d’informations sur le service Base de données SQL, consultez [Présentation de la base de données SQL](sql-database-technical-overview.md). 
* Pour plus d’informations sur les serveurs Azure Database et les fonctionnalités des bases de données, consultez [Fonctionnalités](sql-database-features.md).



<!--HONumber=Feb17_HO3-->


