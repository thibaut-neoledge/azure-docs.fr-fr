---
title: "Stockage des sauvegardes d’Azure SQL Database pendant 10 ans | Microsoft Docs"
description: "Découvrez comment Azure SQL Database prend en charge le stockage de sauvegardes pendant 10 ans."
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/22/2016
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: e47c10c2c979622fba254757d3f7b9f19d3240f8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>Stockage des sauvegardes d’Azure SQL Database pendant 10 ans
De nombreuses applications ont des fins réglementaires, de conformité ou d’autres activités qui vous obligent à conserver les sauvegardes automatiques de la base de données complète au-delà des 7 à 35 jours offerts par les [sauvegardes automatiques](sql-database-automated-backups.md) de SQL Database. La fonctionnalité de **rétention de sauvegarde à long terme** vous permet de stocker vos sauvegardes de l’Azure SQL Database dans un coffre Azure Recovery Services pendant 10 ans. Vous pouvez stocker jusqu'à 1 000 bases de données par coffre. Vous pouvez sélectionner n’importe quelle sauvegarde dans le coffre pour la restaurer en tant que nouvelle base de données.

> [!IMPORTANT]
> La rétention de sauvegarde à long terme est actuellement en version préliminaire et disponible dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Sud du Brésil, États-Unis du Centre, Asie de l’Est, États-Unis de l’Est, États-Unis de l’Est 2, Inde-Centre, Sud de l’Inde, Japon de l’Est, Japon de l’Ouest, Nord du centre des États-Unis, Europe du Nord, Sud du centre des États-Unis, Sud-Est asiatique, Europe de l’Ouest et États-Unis de l’Ouest.
>

> [!NOTE]
> Vous pouvez activer jusqu'à 200 bases de données par coffre pendant une période de 24 heures. Par conséquent, nous vous recommandons d’utiliser un coffre distinct pour chaque serveur afin de minimiser l’impact de cette limite. 
> 



 
## <a name="how-does-sql-database-long-term-backup-retention-work"></a>Comment fonctionne la rétention de sauvegarde SQL Database à long terme ?

La rétention à long terme des sauvegardes vous permet d’associer un serveur Azure SQL Database à un coffre Azure Recovery Services. 

* L’archivage doit être créé dans l’abonnement Azure qui a créé le serveur SQL, dans la même région géographique et dans le même groupe de ressources. 
* Vous configurez ensuite une stratégie de rétention pour une base de données quelconque. La stratégie met en œuvre une copie hebdomadaire des sauvegardes complètes des bases de données dans le coffre Recovery Services et leur conservation pendant la période de rétention spécifiée (jusqu'à 10 ans). 
* Vous pouvez ensuite restaurer l’une de ces sauvegardes dans une nouvelle base de données dans n’importe quel serveur de l’abonnement. La copie est effectuée par le stockage Azure à partir de sauvegardes existantes et n’a aucun impact sur les performances de la base de données existante.

> [!TIP]
> Pour obtenir une procédure, consultez [Configurer et restaurer à partir de la rétention des sauvegardes à long terme Azure SQL Database](sql-database-long-term-backup-retention-configure.md)

## <a name="how-do-i-enable-long-term-backup-retention"></a>Comment activer la rétention de sauvegarde à long terme ?

Pour configurer la rétention de sauvegarde d’une base de données à long terme :

1. Créez un coffre Azure Recovery Services dans la même région, le même abonnement et le même groupe de ressources que votre serveur SQL Database. 
2. Inscrivez le serveur dans le coffre
3. Créez une stratégie de Protection Azure Recovery Services
4. Appliquez la stratégie de protection pour les bases de données nécessitant une rétention à long terme de la sauvegarde

### <a name="azure-portal"></a>Portail Azure

Pour configurer, gérer et restaurer à partir de la rétention des sauvegardes à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec le portail Azure, cliquez sur **Rétention des sauvegardes à long terme**, sélectionnez une base de données, puis cliquez sur **Configurer**. 

   ![sélectionner la base de données pour la rétention des sauvegardes à long terme](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

Pour configurer, gérer et restaurer à partir de la rétention des sauvegardes à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec PowerShell, consultez [Configurer et restaurer à partir de la rétention des sauvegardes à long terme Azure SQL Database](sql-database-long-term-backup-retention-configure.md).

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-backup-retention-feature"></a>Comment restaurer une base de données stockée avec la fonctionnalité de rétention de sauvegarde à long terme ?

Pour restaurer à partir d’une sauvegarde conservée à long terme :

1. Répertoriez le coffre dans lequel la sauvegarde est stockée
2. Répertoriez le conteneur mappé sur votre serveur logique
3. Répertoriez la source de données au sein du coffre mappé sur votre base de données
4. Répertoriez les points de récupération disponibles pour la restauration
5. Restaurez à partir du point de récupération sur le serveur cible dans votre abonnement

Pour configurer, gérer et restaurer depuis la rétention de sauvegarde à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec le portail Azure, consultez [Gestion de la rétention de sauvegarde à long terme avec le portail Azure](sql-database-long-term-backup-retention-configure.md). Pour configurer, gérer et restaurer depuis la rétention de sauvegarde à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec PowerShell, consultez [Gestion de la rétention de sauvegarde à long terme avec PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="how-much-does-long-term-backup-retention-cost"></a>Combien coûte la rétention de sauvegarde à long terme ?

La rétention de sauvegarde à long terme d’une Azure SQL Database est facturée selon la [tarification des services de sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).

Une fois que le serveur Azure SQL Database est inscrit dans le coffre, le stockage total utilisé par les sauvegardes hebdomadaires stockées dans le coffre vous est facturé.

## <a name="view-available-backups-stored-in-long-term-backup-retention"></a>Afficher les sauvegardes disponibles stockées avec rétention à long terme

Pour configurer, gérer et restaurer à partir de la rétention des sauvegardes à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec le portail Azure, consultez [Gérer la rétention des sauvegardes à long terme avec le portail Azure](sql-database-long-term-backup-retention-configure.md). Pour configurer, gérer et restaurer à partir de la rétention des sauvegardes à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services avec PowerShell, consultez [Gérer la rétention des sauvegardes à long terme avec PowerShell](sql-database-long-term-backup-retention-configure.md).

## <a name="disabling-long-term-retention"></a>Désactivation de la rétention à long terme

Recovery Service gère automatiquement le nettoyage des sauvegardes en fonction de la stratégie de rétention fournie. 

* Pour arrêter l’envoi des sauvegardes d’une base de données spécifique dans le coffre, supprimez la stratégie de rétention pour cette base de données.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> Les sauvegardes qui se trouvent déjà dans l’archivage ne sont pas affectées. Elles sont automatiquement supprimées par Recovery Service après l’expiration de leur période de rétention.

## <a name="long-term-backup-retention-faq"></a>Forum Aux Questions sur la rétention des sauvegardes à long terme :

1. Q : Puis-je supprimer manuellement des sauvegardes spécifiques du coffre ?

    R : Pas à ce stade. Le coffre nettoie automatiquement les sauvegardes une fois la période de rétention expirée.
2. Q : Puis-je enregistrer mon serveur pour stocker des sauvegardes dans plus d’un coffre ?

    R : Non, à ce jour vous ne pouvez stocker des sauvegardes que dans un coffre à la fois.
3. Q : Puis-je avoir un serveur et un coffre dans différents abonnements ?

    R : Non. Le coffre et le serveur doivent se trouver dans le même abonnement et dans le même groupe de ressources.
4. Q : Puis-je utiliser un coffre que j’ai créé dans une région différente de celle de mon serveur ?

    R : Non, le coffre et le serveur doivent se trouver dans la même région afin de réduire les délais de copie et d’éviter les frais de trafic.
5. Q : Combien de bases de données puis-je stocker dans un coffre ?

    R : Nous prenons actuellement en charge jusqu'à 1000 de bases de données par coffre. 
6. Q : Combien de coffres puis-je créer par abonnement ?

    R : Vous pouvez créer 25 coffres par abonnement.
7. Q : Combien de bases de données puis-je configurer par jour par coffre ?

    R : Vous ne pouvez configurer que 200 bases de données par jour par coffre.
8. Q : La rétention de sauvegarde à long terme fonctionne-t-elle avec des pools élastiques ?

    R. : Oui. Une base de données dans le pool peut être configurée avec la stratégie de rétention.
9. Q : Puis-je choisir l’heure de création de la sauvegarde ?

    R : Non, SQL Database contrôle la planification des sauvegardes afin de limiter l’impact sur les performances de vos bases de données.
10. Q : J’ai activé TDE pour ma base de données. Puis-je utiliser TDE avec le coffre ? 

    R : Oui, TDE est pris en charge. Même si la base de données d’origine n’existe plus, vous pouvez restaurer la base de données depuis le coffre.
11. Q : Que se passe-t-il avec les sauvegardes dans le coffre si mon abonnement est interrompu ? 

    R : Si votre abonnement est suspendu, nous conservons les bases de données existantes et les sauvegardes. Les nouvelles sauvegardes ne sont pas copiées dans le coffre. Une fois l’abonnement réactivé, le service copie à nouveau les sauvegardes dans le coffre. Vous pouvez effectuer des restaurations à partir de votre coffre à l’aide de sauvegardes copiées avant la suspension de l’abonnement. 
12. Q : Puis-je obtenir un accès aux fichiers de sauvegarde SQL Database afin de les télécharger / restaurer sur SQL Server ?

    R : Non, pas actuellement.
13. Q : Est-il possible d’avoir plusieurs planifications (quotidienne, hebdomadaire, mensuelle, annuelle) au sein d’une stratégie de rétention SQL ?

    R : Non, cette fonctionnalité est actuellement uniquement disponible pour les sauvegardes de machines virtuelles.
14. Q : Que se passe-t-il si nous configurons la rétention à long terme de la sauvegarde sur une base de données qui est une géo-réplication active secondaire ?

    R : Actuellement, nous ne prenons pas en charge les sauvegardes sur des réplicas. Par conséquent, il n’existe aucune option de rétention de sauvegarde à long terme sur les réplicas secondaires. Toutefois, il est important pour un client de configurer la rétention à long terme de la sauvegarde sur une géo-réplication active secondaire pour les raisons suivantes :
    - Quand un basculement a lieu et que la base de données devient primaire, nous procédons à une sauvegarde complète qui est chargée dans un coffre.
    - La configuration de cette rétention de sauvegarde à long terme n’entraîne aucun coût supplémentaire pour le client sur une réplica secondaire.



## <a name="next-steps"></a>Étapes suivantes
Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour en savoir plus sur les autres solutions de continuité des activités Azure SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).


