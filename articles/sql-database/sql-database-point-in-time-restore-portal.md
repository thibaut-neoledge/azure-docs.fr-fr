---
title: "Restaurer une base de données SQL Azure à un point antérieur dans le temps (portail Azure) | Microsoft Docs"
description: "Restaurez une base de données SQL Azure à un point antérieur dans le temps."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: d1822905-a11f-4c42-8940-98c6b81aed20
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feaea525ae1fceff5acdc95fefa115939d5b1da


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restaurer une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-recovery-using-backups.md)
> * [Limite de restauration dans le temps : PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

Cet article explique comment restaurer votre base de données à un point antérieur dans le temps à partir de [sauvegardes automatisées SQL Database](sql-database-automated-backups.md) à l’aide du portail Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Restaurer une base de données SQL à un état antérieur
Sélectionnez une base de données à restaurer dans le portail Azure :

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Sur le côté gauche de l’écran, sélectionnez **Autres services** > **Bases de données SQL**.
3. Cliquez sur la base de données à restaurer.
4. Dans la partie supérieure de la page de votre base de données, sélectionnez **Restaurer** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. Dans la page **Restaurer**, sélectionnez la date et l’heure (au format UTC) auxquelles restaurer la base de données, puis cliquez sur **OK** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Surveiller l’opération de restauration
1. Une fois que vous avez cliqué sur **OK** à l’étape précédente, cliquez sur l’icône de notification dans l’angle supérieur droit de la page, puis cliquez sur la notification **Restauration de la base de données SQL** pour plus d’informations.
   
    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
2. La page Restauration de la base de données SQL s’ouvre avec les informations sur l’état de la restauration. Vous pouvez cliquer sur la ligne pour plus de détails :
   
    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
* Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)  
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


