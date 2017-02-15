---
title: "Vue d’ensemble des outils de gestion SQL Database | Microsoft Docs"
description: "Compare les outils et les options de gestion de bases de données SQL Microsoft Azure."
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
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 915292a191da82af9e24e89460d46dc61a062b18


---
# <a name="overview-management-tools-for-sql-database"></a>Vue d’ensemble des outils de gestion de la base de données SQL
Cette rubrique explore et compare les différents outils et options de gestion des bases de données Azure SQL, afin de vous permettre de choisir l’outil approprié pour le travail, pour votre entreprise et pour vous. Or, la sélection de l’outil adapté dépend du nombre de bases de données que vous gérez, de la tâche effectuée et de la fréquence de son exécution.

## <a name="azure-portal"></a>Portail Azure
Le [portail Azure](https://portal.azure.com) est une application basée sur le web dans laquelle vous pouvez créer, mettre à jour et supprimer des serveurs logiques et des bases de données, et surveiller l’activité des bases de données. Cet outil est très utile si vous n’êtes pas familiarisé avec Microsoft Azure, si vous gérez quelques bases de données ou si vous devez effectuer une action rapidement.

Pour en savoir plus sur l’utilisation du portail, voir [Gérer des bases de données SQL à l’aide du portail Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools et Management Studio dans Visual Studio
Les logiciels SQL Server Management Studio (SSMS) et SQL Server Data Tools (SSDT) sont des outils clients qui s’exécutent sur votre ordinateur pour la gestion et le développement de votre base de données dans le cloud. Si vous êtes un développeur d'applications familiarisé avec Visual Studio ou d'autres environnements de développement intégré (IDE), [essayez la fonction SSDT de Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). De nombreux administrateurs de base de données sont familiarisés avec SSMS, qui peut être utilisé avec les bases de données SQL Azure. [Téléchargez la dernière version de SSMS](https://msdn.microsoft.com/library/mt238290) et utilisez toujours la dernière version quand vous recourez à Azure SQL Database. Pour en savoir plus sur la gestion de vos bases de données SQL Azure avec SSMS, consultez [Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio](sql-database-manage-azure-ssms.md).

> [!IMPORTANT]
> Utilisez toujours les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) et de [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) afin de rester en cohérence avec les mises à jour de Microsoft Azure et SQL Database.
>  

## <a name="powershell"></a>PowerShell
Vous pouvez utiliser PowerShell pour gérer les bases de données et les pools élastiques, et automatiser les déploiements de ressources Azure. Microsoft recommande cet outil pour la gestion d'un grand nombre de bases de données et l'automatisation du déploiement et des modifications apportées aux ressources dans un environnement de production.

Pour plus d’informations, consultez la rubrique [Gérer SQL Database avec PowerShell](sql-database-manage-powershell.md).

## <a name="elastic-database-tools"></a>Outils de base de données élastique
Utilisez les outils de base de données élastique pour effectuer les actions suivantes : 

* Exécution d’un script T-SQL sur un ensemble de bases de données en utilisant une [tâche élastique](sql-database-elastic-jobs-overview.md)
* Déplacement des bases de données d’un modèle multi-locataire dans un modèle à un seul locataire avec [l’outil de fractionnement et de fusion](sql-database-elastic-scale-overview-split-and-merge.md)
* Gestion des bases de données dans un modèle avec un seul locataire ou un modèle multi-locataire avec la [bibliothèque cliente de mise à l’échelle élastique](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Dec16_HO2-->


