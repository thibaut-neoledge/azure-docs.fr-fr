---
title: "Visualiser le point de restauration le plus ancien à partir des sauvegardes d’une base de données Azure SQL Database générées par le service | Microsoft Docs"
description: "Aide-mémoire pour visualiser le point de restauration le plus ancien à partir des sauvegardes d’une base de données générées par le service"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: ae35dd15c386bbbf4e77a143d245efcd1f44f238


---
# <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-an-azure-sql-database"></a>Visualiser le point de restauration le plus ancien à partir des sauvegardes d’une base de données Azure SQL Database générées par le service

Dans ce guide pratique, vous découvrirez comment visualiser le point de restauration le plus ancien à partir des sauvegardes d’une base de données Azure SQL Database générées par le service.

## <a name="view-the-oldest-restore-point-using-the-azure-portal"></a>Visualiser le point de restauration le plus ancien avec le portail Azure

1. Ouvrez le panneau **SQL Database** de votre base de données.

    ![new sample db blade](./media/sql-database-get-started/new-sample-db-blade.png)

2. Dans la barre d’outils, cliquez sur **Restaurer**.

    ![barre d’outils Restaurer](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. Dans le panneau Restaurer, examinez le point de restauration le plus ancien.

    ![point de restauration le plus ancien](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

> [!TIP]
> Pour obtenir un didacticiel, consultez [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez l’article relatif aux [sauvegardes automatiques](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
- Pour plus d’informations sur la restauration à partir de sauvegardes, consultez l’article concernant la [restauration à l’aide de sauvegardes](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


