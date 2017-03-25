---
title: "Portail Azure : Restaurer une base de données SQL Azure | Microsoft Docs"
description: "Restaurez une base de données SQL Azure (portail Azure)."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 49800be1a61e45f64a77f8d2662ca9edeae29de9
ms.lasthandoff: 03/10/2017


---
# <a name="restore-an-azure-sql-database-using-the-azure-portal"></a>Restaurer une base de données SQL Azure à l’aide du portail Azure

Les étapes suivantes montrent comment restaurer une base de données SQL Azure à un point dans le temps, à partir d’une base de données supprimée et à partir d’une sauvegarde géo-redondante.

## <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Restaurer une base de données SQL Azure à un point antérieur dans le temps 

> [!TIP]
> Pour obtenir un didacticiel, consultez [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données](sql-database-get-started-backup-recovery-portal.md)
>

Sélectionnez une base de données à restaurer dans le portail Azure :

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Sur le côté gauche de l’écran, sélectionnez **Autres services** > **Bases de données SQL**.
3. Cliquez sur la base de données à restaurer.
4. Dans la partie supérieure de la page de votre base de données, sélectionnez **Restaurer** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. Dans la page **Restaurer**, sélectionnez la date et l’heure (au format UTC) auxquelles restaurer la base de données, puis cliquez sur **OK** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. Une fois que vous avez cliqué sur **OK** à l’étape précédente, cliquez sur l’icône de notification dans l’angle supérieur droit de la page, puis cliquez sur la notification **Restauration de la base de données SQL** pour plus d’informations.
   
    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. La page Restauration de la base de données SQL s’ouvre avec les informations sur l’état de la restauration. Vous pouvez cliquer sur la ligne pour plus de détails :
   
    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)


## <a name="restore-a-deleted-azure-sql-database-from-backups"></a>Restaurer une base de données SQL Azure supprimée à partir de sauvegardes
Pour restaurer une base de données supprimée dans le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Autres services** > **Serveurs SQL**.
2. Sélectionnez le serveur contenant la base de données à restaurer.
3. Faites défiler jusqu’à la section **Opérations** du panneau de votre serveur, puis sélectionnez **Bases de données supprimées** : ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. Sélectionnez la base de données à restaurer.
5. Spécifiez un nom de base de données, puis cliquez sur **OK** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>Restaurer une base de données Azure SQL à partir d’une sauvegarde géo-redondante

Pour géo-restaurer une base de données dans le portail Azure, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sur le côté gauche de l’écran, sélectionnez **Nouveau** > **Base de données** > **SQL Database** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Sélectionnez **Sauvegarde** comme source, puis la sauvegarde à restaurer. Spécifiez un nom de base de données et le serveur sur lequel restaurer la base de données, puis cliquez sur **Créer** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Surveillez l’état de l’opération de restauration en cliquant sur l’icône de notification dans l’angle supérieur droit de la page.

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
* Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)  
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)


