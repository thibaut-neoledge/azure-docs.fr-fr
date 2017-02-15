---
title: "Restaurer une base de données à partir d’une sauvegarde dans le coffre Azure Recovery Services | Microsoft Docs"
description: "Aide-mémoire sur la restauration d’une base de données pour une sauvegarde dans le coffre Azure Recovery Services et sur l’espace utilisé par ces sauvegardes"
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
ms.openlocfilehash: cdb898ef86517b6eb6eed4b2ae38c4e327e78181


---
# <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restaurer une base de données à partir d’une sauvegarde dans la rétention des sauvegardes à long terme

Dans ce guide pratique, vous apprendrez à restaurer une base de données à partir d’une sauvegarde dans la rétention des sauvegardes à long terme.

## <a name="restore-from-long-term-backup-retention-using-the-azure-portal"></a>Restaurer à partir de la rétention des sauvegardes à long terme avec le Portail Azure

1. Dans le panneau **Azure vault backups (Sauvegardes de coffre Azure)**, cliquez sur la sauvegarde à restaurer, puis cliquez sur **Sélectionner**.

    ![sélectionner une sauvegarde dans le coffre](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Dans la zone de texte **Nom de la base de données**, fournissez le nom de la base de données restaurée.

    ![nom de la nouvelle base de données](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Cliquez sur **OK** pour restaurer votre base de données à partir de la sauvegarde dans le coffre sous la forme d’une nouvelle base de données.

4. Dans la barre d’outils, cliquez sur l’icône de notification pour visualiser l’état du travail de restauration.

    ![progression du travail de restauration à partir du coffre](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Lorsque le travail de restauration est terminé, ouvrez le panneau **Bases de données SQL** pour visualiser la base de données nouvellement restaurée.

    ![base de données restaurée à partir du coffre](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!TIP]
> Pour obtenir un didacticiel, consultez la page [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Étapes suivantes

- Pour configurer la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services, consultez la page [Configurer la rétention des sauvegardes à long terme](sql-database-configure-long-term-retention.md).
- Pour afficher les sauvegardes du coffre Azure Recovery Services, consultez la page [Afficher les sauvegardes avec rétention à long terme](sql-database-view-backups-in-vault.md).
- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez l’article relatif aux [sauvegardes automatiques](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
- Pour plus d’informations sur la restauration à partir de sauvegardes, consultez l’article concernant la [restauration à l’aide de sauvegardes](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


