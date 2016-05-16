<properties
   pageTitle="Récupérer une base de données après une erreur de l’utilisateur dans SQL Data Warehouse | Microsoft Azure"
   description="Procédure de récupération d’une base de données après une erreur de l’utilisateur dans SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/30/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Récupérer une base de données après une erreur de l’utilisateur dans SQL Data Warehouse

SQL Data Warehouse offre deux fonctionnalités de récupération principales lorsqu’une erreur de l’utilisateur entraîne la suppression ou l’endommagement accidentels des données :

- restauration d’une base de données active ;
- restauration d’une base de données supprimée.

Ces deux fonctionnalités restaurent une nouvelle base de données sur le même serveur. Il est important de s'assurer que le serveur sur lequel vous effectuez la restauration a une capacité DTU suffisante pour la nouvelle base de données. Consultez ce blog pour plus d’informations sur [l’affichage et l’augmentation du quota DTU][].

## Restaurer une base de données active
Le service SQL Data Warehouse d’Azure protège toutes les bases de données en direct avec des captures instantanées de la base de données au moins toutes les 8 heures et les conserve pendant 7 jours afin de vous offrir un ensemble discret de points de restauration. Les captures instantanées de la base de données sont également créées lorsque vous suspendez ou supprimez votre base de données. Elles sont conservées pendant 7 jours. En cas d’erreur de l’utilisateur entraînant une modification involontaire des données, vous pouvez restaurer la base de données au niveau de l’un des points de restauration de la période de rétention.

### Portail Azure

Pour restaurer à l’aide du portail Azure, procédez comme suit.

1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Bases de données SQL**
3. Accédez à votre base de données et sélectionnez-la.
4. En haut du panneau de la base de données, cliquez sur **Restaurer**.
5. Spécifiez un nouveau **nom de base de données**, sélectionnez un **point de restauration**, puis cliquez sur **Créer**.
6. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.

### PowerShell

Utilisez Azure PowerShell pour exécuter par programme une restauration de base de données avec l’applet de commande [Restore-AzureRmSqlDatabase][].

> [AZURE.NOTE]  Pour utiliser Azure PowerShell avec SQL Data Warehouse, vous devez installer Azure PowerShell version 1.0.3 ou supérieure. Vous pouvez vérifier la version en exécutant **Get-Module -ListAvailable -Name Azure**. La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][]. Pour plus d’informations sur l’installation de la dernière version, consultez la page [Installation et configuration d’Azure PowerShell][].

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

>[AZURE.NOTE] Pour le serveur nommé foo.database.windows.net, vous devez utiliser « foo » comme nom du serveur dans les applets de commande PowerShell ci-dessus.

### API REST
Utilisez REST pour exécuter par programmation la restauration des bases de données.

1. Suivez la procédure d’obtention de la liste des points de restauration de la base de données.
2. Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][].
3. Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][].

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer la base de données récupérée en suivant le guide [Finaliser une base de données récupérée][].

## Récupérer une base de données supprimée
Avant qu’une base de données ne soit supprimée, Azure SQL Data Warehouse en effectue une capture instantanée et la conserve pendant 7 jours. En cas de suppression accidentelle d’une base de données, vous pouvez restaurer cette dernière telle qu’elle était au moment de la suppression.

### Portail Azure

Pour restaurer une base de données supprimée à l’aide du portail Azure, procédez comme suit.

1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Serveur SQL Server**.
3. Accédez à votre serveur et sélectionnez-le.
4. Dans le panneau du serveur, faites défiler jusqu’à Opérations, puis cliquez sur la vignette **Bases de données supprimées**.
5. Sélectionnez la base de données supprimée que vous souhaitez restaurer.
5. Spécifiez un nouveau **nom pour la base de données** et cliquez sur **Créer**.
6. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.


### PowerShell
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

>[AZURE.NOTE] Pour le serveur nommé foo.database.windows.net, vous devez utiliser « foo » comme nom du serveur dans les applets de commande PowerShell ci-dessus.

### API REST
Utilisez REST pour exécuter par programmation la restauration des bases de données.

1.	Obtenez la liste de toutes vos bases de données supprimées pouvant être restaurées en suivant la procédure [Liste des bases de données supprimées pouvant être restaurées][].
2.	Obtenez des informations détaillées sur la base de données que vous voulez restaurer en suivant la procédure [Obtention de base de données supprimée pouvant être restaurée][].
3.	Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][].
4.	Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][].

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer la base de données récupérée en suivant le guide [Finaliser une base de données récupérée][].

## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: sql-database-business-continuity.md
[Finaliser une base de données récupérée]: sql-database-recovered-finalize.md
[Installation et configuration d’Azure PowerShell]: powershell-install-configure.md

<!--MSDN references-->
[Création d’une demande de restauration de base de données]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Statut d’opération de base de données]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtention de base de données supprimée pouvant être restaurée]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Liste des bases de données supprimées pouvant être restaurées]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[l’affichage et l’augmentation du quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[portail Azure]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0504_2016-->