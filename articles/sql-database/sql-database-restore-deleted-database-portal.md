---
title: "Restaurer une base de données SQL supprimée (portail Azure) | Microsoft Docs"
description: "Restaurez une base de données SQL Azure supprimée (portail Azure)."
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
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5a3f40ff8811c08d130c2a3e0a7d61aed9457d1


---
# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Restaurer une base de données SQL Azure supprimée à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-recovery-using-backups.md)
> * [**Restauration d’une base de données supprimée : Portail**](sql-database-restore-deleted-database-portal.md)
> * [Restauration d’une base de données supprimée : PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## <a name="select-the-database-to-restore"></a>Sélectionner la base de données à restaurer
Pour restaurer une base de données supprimée dans le portail Azure :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Autres services** > **Serveurs SQL**.
2. Sélectionnez le serveur contenant la base de données à restaurer.
3. Faites défiler jusqu’à la section **Opérations** du panneau de votre serveur, puis sélectionnez **Bases de données supprimées** : ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. Sélectionnez la base de données à restaurer.
5. Spécifiez un nom de base de données, puis cliquez sur **OK** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
* Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)  
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


