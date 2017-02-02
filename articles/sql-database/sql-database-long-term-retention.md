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
ms.date: 11/22/2016
ms.author: carlrab; sashan
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 8b13faf1f6cdac355cc4d22b825cc2362a50e8f9


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>Stockage des sauvegardes d’Azure SQL Database pendant 10 ans
De nombreuses applications ont des fins réglementaires, de conformité ou d’autres activités qui vous obligent à conserver les sauvegardes automatiques de la base de données complète au-delà des 7 à 35 jours offerts par les [sauvegardes automatiques](sql-database-automated-backups.md) de SQL Database.

La fonctionnalité de **rétention de sauvegarde à long terme** vous permet de stocker vos sauvegardes de l’Azure SQL Database dans un coffre Azure Recovery Services pendant 10 ans. Vous pouvez stocker jusqu'à 1 000 bases de données par coffre. Vous pouvez sélectionner n’importe quelle sauvegarde dans le coffre pour la restaurer en tant que nouvelle base de données.

> [!NOTE]
> Vous pouvez activer jusqu'à 200 bases de données par coffre pendant une période de 24 heures. Par conséquent, nous vous recommandons d’utiliser un coffre distinct pour chaque serveur afin de minimiser l’impact de cette limite. 
> 
> 

## <a name="how-does-sql-database-long-term-retention-work"></a>Comment fonctionne la rétention de SQL Database à long terme ?

La rétention à long terme des sauvegardes vous permet d’associer un serveur Azure SQL Database à un coffre Azure Recovery Services. 

* L’archivage doit être créé dans l’abonnement Azure qui a créé le serveur SQL, dans la même région géographique et dans le même groupe de ressources. 
* Vous configurez ensuite une stratégie de rétention pour une base de données quelconque. La stratégie met en œuvre une copie hebdomadaire des sauvegardes complètes des bases de données dans le coffre Recovery Services et leur conservation pendant la période de rétention spécifiée (jusqu'à 10 ans). 
* Vous pouvez ensuite restaurer l’une de ces sauvegardes dans une nouvelle base de données dans n’importe quel serveur de l’abonnement. La copie est effectuée par le stockage Azure à partir de sauvegardes existantes et n’a aucun impact sur les performances de la base de données existante.

## <a name="how-do-i-enable-long-term-retention"></a>Comment activer la rétention à long terme ?

Pour configurer la rétention de sauvegarde d’une base de données à long terme :

1. Créez un coffre Azure Recovery Services dans la même région, le même abonnement et le même groupe de ressources que votre serveur SQL Database. 
2. Inscrivez le serveur dans le coffre
3. Créez une stratégie de Protection Azure Recovery Services
4. Appliquez la stratégie de protection pour les bases de données nécessitant une rétention à long terme de la sauvegarde

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-retention-feature"></a>Comment restaurer une base de données stockée avec la fonctionnalité de rétention à long terme ?

Pour restaurer à partir d’une sauvegarde conservée à long terme :

1. Répertoriez le coffre dans lequel la sauvegarde est stockée
2. Répertoriez le conteneur mappé sur votre serveur logique
3. Répertoriez la source de données au sein du coffre mappé sur votre base de données
4. Répertoriez les points de récupération disponibles pour la restauration
5. Restaurez à partir du point de récupération sur le serveur cible dans votre abonnement

## <a name="how-much-does-long-term-retention-cost"></a>Combien coûte la rétention à long terme ?

La rétention à long terme d’une Azure SQL Database est facturée selon la [tarification des services de sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).

Une fois que le serveur Azure SQL Database est inscrit dans le coffre, le stockage total utilisé par les sauvegardes hebdomadaires stockées dans le coffre vous est facturé.

## <a name="configuring-long-term-retention-in-the-azure-portal"></a>Configuration de la rétention à long terme dans le portail Azure

Dans le panneau du serveur Azure SQL Database, vous pouvez configurer la durée de rétention à long terme et, si nécessaire, créer un coffre Azure Recovery Services.

- Pour configurer la rétention à long terme des sauvegardes automatisées dans un coffre Azure Recovery Services, consultez la page [Configurer la rétention des sauvegardes à long terme](sql-database-configure-long-term-retention.md).
- Pour récupérer une base de données à partir d’une sauvegarde avec rétention à long terme, consultez la page [Récupérer à partir d’une sauvegarde avec rétention à long terme](sql-database-restore-from-long-term-retention.md).
- Pour afficher les sauvegardes du coffre Azure Recovery Services, consultez la page [Afficher les sauvegardes avec rétention à long terme](sql-database-view-backups-in-vault.md).

> [!TIP]
> Pour obtenir un didacticiel, consultez la page [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données](sql-database-get-started-backup-recovery.md).
>

## <a name="configuring-long-term-retention-using-powershell"></a>Configuration de la rétention à long terme à l’aide de PowerShell

Procédez comme suit pour configurer la rétention à long terme à l’aide de PowerShell.
1. Créez un coffre Recovery Services
   
   ```
   New-AzureRmResourceGroup -Name $ResourceGroupName –Location 'WestUS' 
   $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName $ResourceGroupName -Location 'WestUS' 
   Set-AzureRmRecoveryServicesBackupProperties   -BackupStorageRedundancy LocallyRedundant  -Vault $vault
   ```
2. Inscrivez votre serveur Azure SQL Database dans le coffre pour que des sauvegardes des bases de données dans le serveur puissent être stockées à long terme.
   
   ```
   Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName 'RG1' -ServerName 'Server1' –ResourceId $vault.Id
   ```
3. Créez une stratégie de rétention pour le stockage des sauvegardes.
   
   ```
   #retrieve the default in-memory policy object for AzureSQLServer workload and set the retention period
   $RP1 = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase
   #Sets the retention value to two years
   $RP1.RetentionDurationType='Years'
   $RP1.RetentionCount=2
   #register the policy for use with any SQL database
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name 'SQLBackup1' –WorkloadType AzureSQLDatabase -retentionPolicy $RP1
   ```
4. Activez la rétention à long terme pour la SQL Database dont vous souhaitez que les sauvegardes soient stockées dans le coffre.
   
   ```
   #for your database you can select any policy created in the vault with which your server is registered
   Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'enabled' -ResourceId $policy.Id
   ```
5. Répertoriez le serveur associé au coffre. Chaque serveur est associé à un conteneur spécifique dans le coffre. Vous pouvez lister les serveurs inscrits en exécutant les commandes suivantes :
   
   ```
   #each server has an associated container in the vault
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL   
   #each database has an associated backup item in the respective container
   Get-AzureRmRecoveryServicesBackupItem –container $container
   ```
6. Répertoriez les bases de données qui ont une stratégie de rétention dans le conteneur. Chaque base de données a un élément de sauvegarde associé dans le conteneur correspondant. Le nom de l’élément de sauvegarde est dérivé du nom de la base de données.
   
    ```
    #list the backup items in the container
    Get-AzureRmRecoveryServicesBackupItem –container $container
    ```

## <a name="restore-from-a-long-term-retention-backup"></a>Restauration à partir d’une sauvegarde conservée à long terme

Procédez comme suit pour restaurer une base de données depuis une sauvegarde dans le coffre Azure Recovery Service :

1. Recherchez le conteneur Recovery Service associé à SQL server.
   
   ```
   #the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
   Set-AzureRMRecoveryServicesVaultContext -Vault $vault
   $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -Name 'Sql;myresourcegroup;myserver'
   ```
2. Recherchez l’élément de sauvegarde associé à une base de données.
   
    ``` 
    #the following command finds the backup item associated with the database 'mydb'
    $item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name 'mydb' 
    ```
3. Recherchez la sauvegarde à partir de laquelle restaurer.
   
   ```
   #The following command lists the backups (also known as the “recovery points”) created in the specific time period.
   $RP=Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item –StartDate '2016-02-01' -EndDate '2016-02-20'
   ```
4. Restaurez à partir du point de récupération dans une nouvelle Azure SQL Database.
   
   ```
   #This command restores from a selected backup. If there are multiple recovery points in the specified range $RP[0] refers to the first one.
   Restore-AzureRMSqlDatabase –FromLongTermRetentionBackup –ResourceId $RP[0].ID TargetResourceGroupName 'RG2' -TargetServerName 'Server2' -TargetDatabaseName 'DB2' [-Edition <String>] [-ServiceObjectiveName <String>] [-ElasticPoolName <String>] [<CommonParameters>]
   ```

## <a name="disabling-long-term-retention"></a>Désactivation de la rétention à long terme

Recovery Service gère automatiquement le nettoyage des sauvegardes en fonction de la stratégie de rétention fournie. 

* Pour arrêter l’envoi des sauvegardes d’une base de données spécifique dans le coffre, supprimez la stratégie de rétention pour cette base de données.
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy –ResourceGroupName 'RG1' –ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> Les sauvegardes qui se trouvent déjà dans l’archivage ne sont pas affectées. Elles sont automatiquement supprimées par Recovery Service après l’expiration de leur période de rétention.
> 
> 

## <a name="removing-backups-from-the-azure-recovery-services-vault"></a>Restauration d’une base de données à partir du coffre Azure Recovery Services

Pour supprimer manuellement des sauvegardes du coffre :

1. Identifiez le conteneur dans le coffre pour « myserver ».
   
    ```
    Set-AzureRMRecoveryServicesVaultContext -Vault $vault 
    $container=Get-AzureRmRecoveryServicesBackupContainer –ContainerType AzureSQL -FriendlyName 'myserver'
    ```
2. Identifiez l’élément de sauvegarde à supprimer.
   
    ``` 
    $item=Get-AzureRmRecoveryServicesBackupItem –container $container -Name 'mydb'
    ```
3. Supprimez les éléments de sauvegarde (toutes les sauvegardes de la base de données « mydb »)
   
    ```
    $job = Disable-AzureRmRecoveryServicesBackupProtection –item $item -Removerecoverypoints 
    Wait-AzureRmRecoveryServicesBackupJob $job
    ```
4. Supprimez le conteneur associé à « myserver »
   
    ```
    Unregister-AzureRmRecoveryServicesBackupContainer –Container $container $container
    ```

## <a name="long-term-retention-faq"></a>Forum aux questions sur la rétention à long terme :

1. Q : Puis-je supprimer manuellement des sauvegardes spécifiques du coffre ?
   R : Pas à ce stade. Le coffre nettoie automatiquement les sauvegardes une fois la période de rétention expirée.
2. Q : Puis-je enregistrer mon serveur pour stocker des sauvegardes dans plus d’un coffre ?
   R : Non, à ce jour vous ne pouvez stocker des sauvegardes que dans un coffre à la fois.
3. Q : Puis-je avoir un serveur et un coffre dans différents abonnements ?
   R : Non. Le coffre et le serveur doivent se trouver dans le même abonnement et dans le même groupe de ressources.
4. Q : Puis-je utiliser un coffre que j’ai créé dans une région différente de celle de mon serveur ?
   R : Non, le coffre et le serveur doivent se trouver dans la même région afin de réduire les délais de copie et d’éviter les frais de trafic.
5. Q : Combien de bases de données puis-je stocker dans un coffre ?
   R : Nous prenons actuellement en charge jusqu'à 1 000 de bases de données par coffre. 
6. Q : Combien de coffres puis-je créer par abonnement ? R : Vous pouvez créer 25 coffres par abonnement.
7. Q : Combien de bases de données puis-je configurer par jour par coffre ? A : Vous ne pouvez configurer que 200 bases de données par jour par coffre.
8. Q : La rétention à long terme fonctionne-t-elle avec des pools élastiques ?
   R. : Oui. Une base de données dans le pool peut être configurée avec la stratégie de rétention.
9. Q : Puis-je choisir l’heure de création de la sauvegarde ?
   R : Non, SQL Database contrôle la planification des sauvegardes afin de limiter l’impact sur les performances de vos bases de données.
10. Q : J’ai activé TDE pour ma base de données. Puis-je utiliser TDE avec le coffre ? R : Oui, TDE est pris en charge. Même si la base de données d’origine n’existe plus, vous pouvez restaurer la base de données depuis le coffre.
11. Q : Que se passe-t-il avec les sauvegardes dans le coffre si mon abonnement est interrompu ? R : Si votre abonnement est suspendu, nous conservons les bases de données et les sauvegardes existantes, mais les nouvelles sauvegardes ne sont pas copiées dans le coffre. Une fois l’abonnement réactivé, le service copie à nouveau les sauvegardes dans le coffre. Vous pouvez effectuer des restaurations à partir de votre coffre à l’aide de sauvegardes copiées avant la suspension de l’abonnement. 
12. Q : Puis-je obtenir un accès aux fichiers de sauvegarde SQL Database afin de les télécharger / restaurer sur SQL Server ?
   R : Non, pas actuellement.
13. Q : Est-il possible d’avoir plusieurs planifications (quotidienne, hebdomadaire, mensuelle, annuelle) au sein d’une stratégie de rétention SQL ?
   R : Non, cette fonctionnalité est actuellement uniquement disponible pour les sauvegardes de machines virtuelles.

## <a name="next-steps"></a>Étapes suivantes
Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour en savoir plus sur les autres solutions de continuité des activités Azure SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).




<!--HONumber=Dec16_HO2-->


