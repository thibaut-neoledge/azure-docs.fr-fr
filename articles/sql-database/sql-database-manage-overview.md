---
title: "Outils de gestion et de développement avec Azure SQL Database | Microsoft Docs"
description: "Présente les options et outils de gestion et de développement pour Azure SQL Database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 9b0a3a314e576db2133e5c63fada43bb11a4e520
ms.lasthandoff: 03/04/2017


---
# <a name="overview-tools-to-manage--develop-with-azure-sql-database"></a>Vue d’ensemble : outils de gestion et de développement avec Azure SQL Database
Cette rubrique présente les outils de gestion et de développement avec des bases de données SQL Azure.

> [!IMPORTANT]
> Cet ensemble de documents comprend des guides de démarrage rapide, des exemples et des guides pratiques qui vous montrent comment gérer et développer avec Azure SQL Database en utilisant les outils présentés dans les paragraphes suivants. Utilisez la zone de filtre et le volet de navigation de gauche pour rechercher des contenus propres au Portail Azure, à PowerShell et à T-SQL.
>

## <a name="azure-portal"></a>Portail Azure
Le [portail Azure](https://portal.azure.com) est une application basée sur le web dans laquelle vous pouvez créer, mettre à jour et supprimer des serveurs logiques et des bases de données, et surveiller l’activité des bases de données. Cet outil est très utile si vous n’êtes pas familiarisé avec Microsoft Azure, si vous gérez quelques bases de données ou si vous devez effectuer une action rapidement.

## <a name="sql-server-management-studio-and-transact-sql"></a>SQL Server Management Studio et Transact-SQL
SQL Server Management Studio (SSMS) est un outil client qui s’exécute sur votre ordinateur pour gérer votre base de données dans le cloud avec Transact-SQL. De nombreux administrateurs de base de données sont familiarisés avec SSMS, qui peut être utilisé avec les bases de données SQL Azure. [Téléchargez la dernière version de SSMS](https://msdn.microsoft.com/library/mt238290) et utilisez toujours la dernière version quand vous recourez à Azure SQL Database. 

> [!IMPORTANT]
> Utilisez toujours la dernière version de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290).
>  

## <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools dans Visual Studio
SQL Server Data Tools (SSDT) est un outil client qui s’exécute sur votre ordinateur pour développer votre base de données dans le cloud. Si vous êtes un développeur d'applications familiarisé avec Visual Studio ou d'autres environnements de développement intégré (IDE), [essayez la fonction SSDT de Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx).  

> [!IMPORTANT]
> Utilisez toujours la dernière version de [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) pour rester synchronisé avec les mises à jour de Microsoft Azure et de SQL Database.
>  
## <a name="powershell"></a>PowerShell
Vous pouvez utiliser PowerShell pour gérer les bases de données et les pools élastiques, et automatiser les déploiements de ressources Azure. Microsoft recommande cet outil pour la gestion d'un grand nombre de bases de données et l'automatisation du déploiement et des modifications apportées aux ressources dans un environnement de production.

## <a name="elastic-database-tools"></a>Outils de base de données élastique
Utilisez les outils de base de données élastique pour effectuer les actions suivantes : 

* Exécution d’un script T-SQL sur un ensemble de bases de données en utilisant une [tâche élastique](sql-database-elastic-jobs-overview.md)
* Déplacement des bases de données d’un modèle multi-locataire dans un modèle à un seul locataire avec [l’outil de fractionnement et de fusion](sql-database-elastic-scale-overview-split-and-merge.md)
* Gestion des bases de données dans un modèle avec un seul locataire ou un modèle multi-locataire avec la [bibliothèque cliente de mise à l’échelle élastique](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)


