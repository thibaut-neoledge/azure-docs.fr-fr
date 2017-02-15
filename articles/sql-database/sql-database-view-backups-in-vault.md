---
title: Afficher les sauvegardes dans le coffre Azure Recovery Services | Microsoft Docs
description: "Aide-mémoire sur l’affichage des sauvegardes dans le coffre Azure Recovery Services et sur l’espace utilisé par ces sauvegardes"
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
ms.openlocfilehash: 723be587128c131670c9905572d808c176362fb8


---
# <a name="view-information-about-your-database-backups-in-long-term-backup-retention"></a>Visualiser les informations relatives à vos sauvegardes de base de données dans la fonctionnalité de rétention des sauvegardes à long terme

Dans ce guide pratique, vous apprendrez à visualiser les informations relatives aux sauvegardes de votre base de données dans la fonctionnalité de rétention des sauvegardes à long terme.

## <a name="view-long-term-backup-retention-information-using-the-azure-portal"></a>Afficher des informations sur la rétention des sauvegardes à long terme avec le Portail Azure 

1. Ouvrez le panneau du coffre Azure Recovery Services (accédez à **Toutes les ressources**, puis sélectionnez cette entrée dans la liste des ressources de votre abonnement) pour visualiser la quantité de stockage utilisée par les sauvegardes de votre base de données dans le coffre.

   ![visualiser le coffre Recovery Services avec les sauvegardes](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Ouvrez le panneau **Base de données SQL** de votre base de données.

    ![new sample db blade](./media/sql-database-get-started/new-sample-db-blade.png)

3. Dans la barre d’outils, cliquez sur **Restaurer**.

    ![barre d’outils Restaurer](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Dans le panneau Restaurer, cliquez sur **À long terme**.

5. Sous Azure vault backups (Sauvegardes de coffre Azure), cliquez sur **Sélectionner une sauvegarde** pour visualiser les sauvegardes de base de données disponibles dans la rétention des sauvegardes à long terme.

    ![sauvegardes dans le coffre](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

> [!TIP]
> Pour obtenir un didacticiel, consultez la page [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Étapes suivantes

- Pour configurer la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services, voir [](sql-database-configure-long-term-retention.md).
- Pour restaurer une base de données à partir d’une sauvegarde dans la rétention des sauvegardes à long terme, consultez la page [Restaurer à partir de la rétention à long terme](sql-database-restore-from-long-term-retention.md).
- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez la page [Sauvegardes automatiques](sql-database-automated-backups.md).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
- Pour plus d’informations sur la restauration à partir de sauvegardes, consultez l’article concernant la [restauration à l’aide de sauvegardes](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


