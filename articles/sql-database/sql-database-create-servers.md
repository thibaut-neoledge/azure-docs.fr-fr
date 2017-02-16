---
title: "Création de serveurs Azure SQL Database | Microsoft Docs"
description: "Référence rapide sur la création de serveurs Azure SQL Database à l’aide du portail Azure et de PowerShell."
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
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: e8fe805da2446895b6616926fe1326dc30b9d0d0


---

# <a name="create-azure-sql-database-servers"></a>Création de serveurs Azure SQL Database

Vous pouvez créer un serveur Azure SQL Database à l’aide du [portail Azure](https://portal.azure.com/), de PowerShell, de l’API REST ou de C#. 

## <a name="create-an-azure-sql-database-server-using-the-azure-portal"></a>Création d’un serveur Azure SQL Database à l’aide du portail Azure

1. Ouvrez le panneau **Serveurs SQL** dans le [portail Azure](https://portal.azure.com/). 

    ![Serveurs SQL](./media/sql-database-get-started/new-sql-server.png)

2. Cliquez sur **Ajouter** pour créer un serveur SQL

    ![Ajouter un serveur SQL](./media/sql-database-get-started/new-sql-server-add.png)

> [!TIP]
> Pour un didacticiel de prise en main avec le portail Azure et SQL Server Management Studio, consultez [Prise en main des serveurs Azure SQL Database, des bases de données et des règles de pare-feu à l’aide du portail Azure et de SQL Server Management Studio](sql-database-get-started.md).
>

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
> Pour obtenir un exemple de script, consultez [Créer un script PowerShell de base de données SQL](sql-database-get-started-powershell.md).
>

## <a name="additional-resources"></a>Ressources supplémentaires
* Pour une vue d’ensemble des outils de gestion, consultez [Vue d’ensemble des outils de gestion](sql-database-manage-overview.md)
* Pour découvrir comment effectuer des tâches de gestion à l’aide du portail Azure, consultez [Gérer des bases de données SQL Azure au moyen du portail Azure](sql-database-manage-portal.md)
* Pour découvrir comment effectuer des tâches de gestion à l’aide de PowerShell, consultez [Gérer des bases de données SQL Azure au moyen de PowerShell](sql-database-manage-powershell.md)
* Pour découvrir comment effectuer des tâches supplémentaires à l’aide de SQL Server Management Studio, consultez [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Pour plus d’informations sur le service Base de données SQL, consultez [Présentation de la base de données SQL](sql-database-technical-overview.md). 
* Pour plus d’informations sur les serveurs Azure Database et les fonctionnalités des bases de données, consultez [Fonctionnalités](sql-database-features.md).



<!--HONumber=Dec16_HO3-->


