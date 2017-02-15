---
title: "Configurer la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services | Microsoft Docs"
description: "Aide-mémoire pour configurer la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services"
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
ms.openlocfilehash: b395fc5c1deaf04205dcfccacd13d30ec90dd1e7


---
# <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Configurer la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services 

Dans ce guide pratique, vous apprendrez à configurer la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services.

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

14. Une fois la rétention des sauvegardes à long terme activée, ouvrez le panneau **Coffre Recovery Services** (accédez à **Toutes les ressources**, puis sélectionnez cette entrée dans la liste des ressources de votre abonnement).

   ![visualiser le coffre Recovery Services](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)


> [!TIP]
> Pour obtenir un didacticiel, consultez la page [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données](sql-database-get-started-backup-recovery.md).
>

## <a name="next-steps"></a>Étapes suivantes

- Pour récupérer une base de données à partir d’une sauvegarde avec rétention à long terme, consultez la page [Récupérer à partir d’une sauvegarde avec rétention à long terme](sql-database-restore-from-long-term-retention.md).
- Pour afficher les sauvegardes du coffre Azure Recovery Services, consultez la page [Afficher les sauvegardes avec rétention à long terme](sql-database-view-backups-in-vault.md).
- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez l’article relatif aux [sauvegardes automatiques](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
- Pour plus d’informations sur la restauration à partir de sauvegardes, consultez l’article concernant la [restauration à l’aide de sauvegardes](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO2-->


