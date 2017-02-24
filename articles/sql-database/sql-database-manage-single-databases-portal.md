---
title: "Portail Azure : créer et gérer des bases de données SQL Azure uniques | Microsoft Docs"
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
ms.sourcegitcommit: 319e22e2fb423e0fe1fabf95d0018d4fa3232e0b
ms.openlocfilehash: 5d7f11b9b027f86adf3d2382e82f2d382d71ee51


---
# <a name="create-and-manage-single-azure-sql-databases-with-the-azure-portal"></a>Créer et gérer des bases de données SQL Azure uniques avec le Portail Azure

Vous pouvez créer et gérer des bases de données SQL Azure uniques avec le [Portail Azure](https://portal.azure.com/), PowerShell, Transact-SQL, l’API REST ou C#. Cette rubrique porte sur l’utilisation du Portail Azure. Pour PowerShell, consultez la page [Créer et gérer des bases de données uniques avec PowerShell](sql-database-manage-single-databases-powershell.md). Pour Transact-SQL, consultez la page [Créer et gérer des bases de données uniques avec Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-a-single-azure-sql-database-with-the-azure-portal"></a>Créer une base de données SQL Azure unique avec le Portail Azure

1. Ouvrez le panneau **Bases de données SQL** dans le [portail Azure](https://portal.azure.com/). 

    ![bases de données SQL](./media/sql-database-get-started/sql-databases.png)
2. Dans le panneau de bases de données SQL, cliquez sur **Ajouter**.

    ![add sql database](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Vous trouverez un didacticiel de création d’une base de données avec le Portail Azure à la page [Créer une base de données - Portail Azure](sql-database-get-started.md).
>    

## <a name="view-and-update-sql-database-settings-using-the-azure-portal"></a>Affichage et mise à jour des paramètres d’une base de données SQL à l’aide du portail Azure

1. Ouvrez le panneau **Bases de données SQL** dans le [portail Azure](https://portal.azure.com/). 

    ![bases de données SQL](./media/sql-database-get-started/sql-databases.png)

2. Cliquez sur la base de données que vous souhaitez utiliser, puis cliquez sur le paramètre souhaité dans le panneau Base de données SQL.

    ![new sample db blade](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Modifier les niveaux de service et de performances d’une base de données unique
Ouvrez le panneau SQL Database de la base de données dont vous souhaitez augmenter ou diminuer la taille :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Autres services** > **Bases de données SQL**.
2. Cliquez sur la base de données à modifier.
3. Dans le panneau **Base de données SQL**, cliquez sur **Niveau tarifaire (mise à l’échelle de DTU)** :
   
   ![Niveau de tarification](./media/sql-database-manage-single-database-portal/new-tier.png)

4. Sélectionnez un nouveau niveau, puis cliquez sur **Sélectionner** :
   
   Le fait de cliquer sur **Sélectionner** envoie une demande de mise à l’échelle pour modifier le niveau de tarification. En fonction de la taille de votre base de données, l’opération de mise à l’échelle peut prendre plus ou moins longtemps (voir les informations en haut de cet article).
   
   > [!NOTE]
   > La modification de la tarification de votre base de données ne modifie pas la taille maximale de la base de données. Pour modifier la taille maximale de votre base de données, utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
   > 
   > 
   
   ![sélectionner un niveau de tarification](./media/sql-database-manage-single-database-portal/choose-tier.png)
5. Cliquez sur l’icône de notification (cloche), dans le coin supérieur droit :
   
   ![Notifications](./media/sql-database-manage-single-database-portal/scale-notification.png)
   
6. Cliquez sur le texte de notification pour ouvrir le volet d’informations où vous pouvez afficher l’état de la demande.

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble des outils de gestion, consultez [Vue d’ensemble des outils de gestion](sql-database-manage-overview.md).
* Pour découvrir comment effectuer des tâches de gestion à l’aide du portail Azure, consultez [Gérer des bases de données SQL Azure au moyen du portail Azure](sql-database-manage-portal.md).
* Pour découvrir comment effectuer des tâches de gestion à l’aide de PowerShell, consultez [Gérer des bases de données SQL Azure au moyen de PowerShell](sql-database-manage-powershell.md).
* Pour découvrir comment effectuer des tâches de gestion à l’aide de SQL Server Management Studio, consultez [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Pour plus d’informations sur le service Base de données SQL, consultez [Présentation de la base de données SQL](sql-database-technical-overview.md). 
* Pour plus d’informations sur les serveurs Azure Database et les fonctionnalités des bases de données, consultez [Fonctionnalités](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


