---
title: "Portail Azure : gestion de la rétention de sauvegardes de base de données à long terme | Microsoft Docs"
description: "Aide-mémoire pour configurer, gérer et restaurer depuis la rétention à long terme des sauvegardes de bases de données Azure SQL automatisées dans un coffre Azure Recovery Services avec le portail Azure"
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
ms.date: 12/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: fa4cf56c10f885a2fd4b62e08a369a51d1280ab3
ms.lasthandoff: 03/25/2017


---
# <a name="configure-manage-and-restore-from-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-the-azure-portal"></a>Configurer, gérer et restaurer depuis la rétention à long terme des sauvegardes de base de données dans un coffre Azure Recovery Services avec le portail Azure

Dans cette rubrique, vous apprendrez à configurer, gérer et restaurer depuis la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec le portail Azure. Vous pouvez également effectuer cette étape à l’aide de [PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).

Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).

> [!TIP]
> Pour obtenir un didacticiel, consultez [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données à l’aide du portail Azure](sql-database-get-started-backup-recovery-portal.md).
>

## <a name="configure-long-term-retention-using-the-azure-portal"></a>Configurer la rétention à long terme avec le Portail Azure

1. Ouvrez le panneau **SQL Server** de votre serveur.

    ![sql server blade](./media/sql-database-get-started/sql-server-blade.png)

2. Cliquez sur **Long-term backup retention (Rétention des sauvegardes à long terme)**.

   ![lien de rétention des sauvegardes à long terme](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Dans le panneau **Long-term backup retention** (Rétention des sauvegardes à long terme), examinez et acceptez les conditions d’utilisation de la version préliminaire (à moins que vous ne l’ayez déjà fait ou que cette fonctionnalité ne soit plus en version préliminaire).

   ![accepter les conditions d’utilisation de la version préliminaire](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Pour configurer la rétention des sauvegardes à long terme d’une base de données, sélectionnez cette base de données dans la grille, puis cliquez sur **Configurer** dans la barre d’outils.

   ![sélectionner la base de données pour la rétention des sauvegardes à long terme](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Dans le panneau **Configurer**, cliquez sur **Configurer les paramètres requis** sous **Coffre Recovery Services**.

   ![lien de configuration du coffre](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Dans le panneau **Coffre Recovery Services**, sélectionnez un éventuel coffre existant. Dans le cas contraire, si aucun coffre Recovery Services n’a été trouvé pour votre abonnement, cliquez pour quitter le flux, puis créez un coffre Recovery Services.

   ![lien de création d’un coffre](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Dans le panneau **Coffres Recovery Services**, cliquez sur **Ajouter**.

   ![lien d’ajout d’un coffre](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. Dans le panneau **Coffre Recovery Services**, fournissez un nom valide pour le nouveau coffre Recovery Services.

   ![nom du nouveau coffre](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Sélectionnez votre abonnement et votre groupe de ressources, puis sélectionnez l’emplacement du coffre. Une fois que vous avez terminé, cliquez sur **Créer**.

   ![créer un coffre](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > Le coffre doit se trouver dans la même région que le serveur logique SQL Azure et doit utiliser le même groupe de ressources que le serveur logique.
   >

10. Une fois le nouveau coffre créé, exécutez les étapes nécessaires pour revenir au panneau **Coffre Recovery Services**.

11. Dans le panneau **Coffre Recovery Services**, cliquez sur le coffre, puis sur **Sélectionner**.

   ![sélectionner un coffre existant](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Dans le panneau **Configurer**, fournissez un nom valide pour la nouvelle stratégie de rétention, modifiez la stratégie de rétention par défaut à votre convenance, puis cliquez sur **OK**.

   ![définir la stratégie de rétention](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. Dans le panneau **Long-term backup retention** (Rétention des sauvegardes à long terme), cliquez sur **Enregistrer**, puis sur **OK** pour appliquer la stratégie de rétention des sauvegardes à long terme à toutes les bases de données sélectionnées.

   ![définir la stratégie de rétention](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Cliquez sur **Enregistrer** pour activer la rétention des sauvegardes à long terme à l’aide de cette nouvelle stratégie dans le coffre Azure Recovery Services que vous avez configuré.

   ![définir la stratégie de rétention](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)


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
> Pour obtenir un didacticiel, consultez la page [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données](sql-database-get-started-backup-recovery-portal.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour gérer la rétention à long terme des sauvegardes avec PowerShell, consultez [Gestion de la rétention de sauvegarde à long terme avec PowerShell](sql-database-manage-long-term-backup-retention-powershell.md)
- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez la page [Sauvegardes automatiques](sql-database-automated-backups.md).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
- Pour plus d’informations sur la restauration à partir de sauvegardes, consultez l’article concernant la [restauration à l’aide de sauvegardes](sql-database-recovery-using-backups.md).
