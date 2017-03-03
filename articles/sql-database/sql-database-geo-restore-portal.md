---
title: "Portail Azure : restauration d’une base de données SQL à partir d’une sauvegarde géo-redondante | Microsoft Docs"
description: "Restaurer une base de données Azure SQL Database sur un nouveau serveur à partir d’une sauvegarde géo-redondante avec le portail Azure"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 7d9314444c39cda3f9d893e4f97b4afce4c75777
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-the-azure-portal"></a>Restauration d’une base de données Azure SQL Database à partir d’une sauvegarde géo-redondante à l’aide du portail Azure

Cet article vous explique comment restaurer votre base de données sur un nouveau serveur à l’aide de la géo-restauration avec le portail Azure. Cette tâche peut également être effectuée [avec PowerShell](sql-database-geo-restore-powershell.md).

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Restaurer une base de données Azure SQL Database à partir d’une sauvegarde géo-redondante à l’aide du portail Azure

Pour géo-restaurer une base de données dans le portail Azure, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Sur le côté gauche de l’écran, sélectionnez **Nouveau** > **Base de données** > **SQL Database** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Sélectionnez **Sauvegarde** comme source, puis la sauvegarde à restaurer. Spécifiez un nom de base de données et le serveur sur lequel restaurer la base de données, puis cliquez sur **Créer** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Surveillez l’état de l’opération de restauration en cliquant sur l’icône de notification dans l’angle supérieur droit de la page.


## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
* Pour en savoir plus sur les sauvegardes automatisées Azure SQL Database, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md).
* Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md).  
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md).


