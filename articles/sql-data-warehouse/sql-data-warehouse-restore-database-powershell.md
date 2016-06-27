<properties
   pageTitle="Restauration d’un Azure SQL Data Warehouse (PowerShell) | Microsoft Azure"
   description="Tâches PowerShell permettant de restaurer un Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/11/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Restauration d’un Azure SQL Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Vue d'ensemble][]
- [Portail][]
- [PowerShell][]
- [REST][]

Dans cet article, vous allez apprendre à restaurer un Azure SQL Data Warehouse à l’aide de PowerShell.

## Avant de commencer

### Vérifiez la capacité DTU de votre base de données SQL. 

Chaque SQL Data Warehouse est hébergé par un serveur SQL logique. Ce serveur logique a une limite de capacité mesurée en DTU. Avant de pouvoir restaurer un SQL Data Warehouse, il est important de s’assurer que le serveur SQL logique qui héberge votre base de données a une capacité de DTU suffisante pour la base de données en cours de restauration. Consultez ce blog pour plus d’informations sur [l’affichage et l’augmentation du quota de DTU][].

### Installer PowerShell

Pour utiliser Azure PowerShell avec SQL Data Warehouse, vous devez installer Azure PowerShell version 1.0 ou supérieure. Vous pouvez vérifier la version en exécutant **Get-Module -ListAvailable -Name Azure**. La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][]. Pour plus d’informations sur l’installation de la dernière version, consultez la page [Installation et configuration d’Azure PowerShell][].

## Restauration d’une base de données active ou en pause

Pour restaurer une base de données à partir d’un instantané, utilisez l’applet de commande PowerShell [Restore-AzureRmSqlDatabase][].

1. Ouvrez Windows PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données à restaurer.
4. Répertoriez les points de restauration pour la base de données.
5. Sélectionnez le point de restauration souhaité à l’aide de l’élément RestorePointCreationDate.
6. Restaurez la base de données au niveau du point de restauration souhaité.
7. Vérifiez que la base de données restaurée est en ligne.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

Une fois la restauration terminée, vous pouvez configurer la base de données récupérée en suivant le guide [Finaliser une base de données récupérée][].

## restauration d’une base de données supprimée.

Pour restaurer une base de données supprimée, utilisez l’applet de commande [Restore-AzureRmSqlDatabase][].

1. Ouvrez Windows PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données supprimée à restaurer.
4. Accédez à la base de données supprimée.
5. Restaurez la base de données supprimée.
6. Vérifiez que la base de données restaurée est en ligne.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

Une fois la restauration terminée, vous pouvez configurer la base de données récupérée en suivant le guide [Finalize a recovered database][] (Finaliser une base de données récupérée).

## Restauration à partir d’une région géographique Azure

Pour restaurer une base de données, utilisez l’applet de commande [Restore-AzureRmSqlDatabase][].

1. Ouvrez Windows PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données à restaurer.
4. Obtenez la base de données à récupérer.
5. Créez la demande de récupération de la base de données.
6. Vérifiez l’état de la base de données affectée par la géo-restauration.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

### Configurer votre base de données après avoir effectué une géo-restauration

Voici une liste de contrôle pour vous aider à préparer votre base de données de production récupérée.

1. **Mettre à jour les chaînes de connexion** : vérifiez que les chaînes de connexion de vos outils clients pointent vers la base de données qui vient d’être récupérée.
2. **Modifier les règles de pare-feu** : vérifiez les règles de pare-feu sur le serveur cible. Assurez-vous également que les connexions entre les ordinateurs clients ou Azure et le serveur et la base de données qui vient d’être récupérée sont activées.
3. **Vérifier les connexions de serveur et les utilisateurs des bases de données** : vérifiez que toutes les connexions utilisées par votre application existent sur le serveur qui héberge votre base de données récupérée. Créez à nouveau les connexions manquantes et accordez-leur les autorisations appropriées sur la base de données restaurée. 
4. **Activer l’audit** : si la fonction d’audit est nécessaire pour accéder à votre base de données, vous devez l’activer après la récupération de la base de données.

La base de données récupérée sera compatible avec le chiffrement transparent des données si la base de données source l’est aussi.


## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: sql-database-business-continuity.md
[Finalize a recovered database]: sql-database-recovered-finalize.md
[Finaliser une base de données récupérée]: sql-database-recovered-finalize.md
[Installation et configuration d’Azure PowerShell]: powershell-install-configure.md
[Vue d'ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[l’affichage et l’augmentation du quota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0615_2016-->