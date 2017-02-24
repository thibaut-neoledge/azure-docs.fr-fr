---
title: "T-SQL : créer et gérer des bases de données SQL Azure uniques | Microsoft Docs"
description: "Aide-mémoire sur la création et la gestion de bases de données SQL Azure uniques avec le Portail Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 38c53d6f1a2635afbba199e6f0a2adb23abb6a3a
ms.openlocfilehash: fcf8326edc2ff84d274f7c4dadeae1fbe2595349


---
# <a name="create-and-manage-single-azure-sql-databases-with-transact-sql"></a>Créer et gérer des bases de données SQL Azure uniques avec Transact-SQL

Vous pouvez créer et gérer des bases de données SQL Azure uniques avec le [Portail Azure](https://portal.azure.com/), PowerShell, Transact-SQL, l’API REST ou C#. Cette rubrique porte sur l’utilisation du Portail Azure. Pour PowerShell, consultez la page [Créer et gérer des bases de données uniques avec PowerShell](sql-database-manage-single-databases-powershell.md). Pour Transact-SQL, consultez la page [Créer et gérer des bases de données uniques avec Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>Création d’une base de données SQL Azure à l’aide de Transact-SQL dans SQL Server Management Studio

Pour créer une base de données SQL à l’aide de Transact-SQL dans SQL Server Management Studio :

1. À l’aide de SQL Server Management Studio, connectez-vous au serveur de bases de données Azure à l’aide de la connexion principale au niveau du serveur ou d’une connexion qui est membre du rôle **dbmanager**. Pour plus d’informations sur les connexions, consultez [Gérer les connexions](sql-database-manage-logins.md).
2. Dans l’Explorateur d’objets, ouvrez le nœud Bases de données, développez le dossier **Bases de données système**, cliquez avec le bouton droit sur **master**, puis cliquez sur **Nouvelle requête**.
3. Utilisez l’instruction **CREATE DATABASE** pour créer une base de données. Pour plus d'informations, consultez la rubrique [CREATE DATABASE (Base de données SQL)](https://msdn.microsoft.com/library/dn268335.aspx). L’instruction suivante crée une base de données appelée **maDBTest** , et spécifie qu’il s’agit d’une base de données Standard S0 Edition d’une taille maximale de 250 Go.
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. Cliquez sur **Exécuter** pour exécuter la requête.
5. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur le nœud Bases de données et cliquez sur **Actualiser** pour afficher la nouvelle base de données dans l’Explorateur d’objets. 


## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Modifier les niveaux de service et de performances d’une base de données unique
Modifier la taille maximale de votre base de données avec [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx)

> [!TIP]
> Vous trouverez un didacticiel de création d’une base de données avec Transact-SQL à la page [Créer une base de données - Portail Azure](sql-database-get-started.md).
>

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble des outils de gestion, consultez [Vue d’ensemble des outils de gestion](sql-database-manage-overview.md).
* Pour découvrir comment effectuer des tâches de gestion à l’aide du portail Azure, consultez [Gérer des bases de données SQL Azure au moyen du portail Azure](sql-database-manage-portal.md).
* Pour découvrir comment effectuer des tâches de gestion à l’aide de PowerShell, consultez [Gérer des bases de données SQL Azure au moyen de PowerShell](sql-database-manage-powershell.md).
* Pour découvrir comment effectuer des tâches de gestion à l’aide de SQL Server Management Studio, consultez [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Pour plus d’informations sur le service Base de données SQL, consultez [Présentation de la base de données SQL](sql-database-technical-overview.md). 
* Pour plus d’informations sur les serveurs Azure Database et les fonctionnalités des bases de données, consultez [Fonctionnalités](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


