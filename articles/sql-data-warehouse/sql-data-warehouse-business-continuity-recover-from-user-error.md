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
   ms.date="09/23/2015"
   ms.author="sahajs"/>

# Récupérer une base de données après une erreur de l’utilisateur dans SQL Data Warehouse

SQL Data Warehouse offre deux fonctionnalités de récupération principales lorsqu’une erreur de l’utilisateur entraîne la suppression ou l’endommagement accidentels des données :

- restauration d’une base de données active ;
- restauration d’une base de données supprimée.

Ces deux fonctionnalités restaurent une nouvelle base de données sur le même serveur.

## Restaurer une base de données active
En cas d’erreur de l’utilisateur entraînant une modification involontaire des données, vous pouvez restaurer la base de données au niveau de l’un des points de restauration de la période de rétention. Les captures instantanées d’une base de données active se produisent toutes les 8 heures et sont conservées pendant 7 jours.

### PowerShell

Utilisez Azure PowerShell pour exécuter par programmation la restauration d’une base de données. Pour télécharger le module Azure PowerShell, exécutez [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).

Pour restaurer une base de données, utilisez l’applet de commande [Start-AzureSqlDatabaseRestore][].

1. Ouvrez Microsoft Azure PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données à restaurer.
4. Répertoriez les points de restauration de la base de données (cette opération nécessite le mode de gestion des ressources Azure).
5. Sélectionnez le point de restauration souhaité à l’aide de l’élément RestorePointCreationDate.
6. Restaurez la base de données au niveau du point de restauration souhaité.
7. Surveillez la progression de la restauration.

```

Add-AzureAccount
Get-AzureSubscription
Select-AzureSubscription -SubscriptionName "<Subscription_name>"

# List database restore points
Switch-AzureMode AzureResourceManager
Get-AzureSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
Switch-AzureMode AzureServiceManagement
$Database = Get-AzureSqlDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Notez que si votre serveur est nommé foo.database.windows.net, vous devez utiliser « foo »comme nom du serveur dans les applets de commande Powershell ci-dessus.

### API REST
Utilisez REST pour exécuter par programmation la restauration des bases de données.

1. Suivez la procédure d’obtention de la liste des points de restauration de la base de données.
2. Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][].
3. Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][].

Une fois la restauration terminée, vous pouvez configurer la base de données récupérée à utiliser en suivant le guide [Finaliser une base de données récupérée][].

## Récupérer une base de données supprimée
En cas de suppression d’une base de données, vous pouvez restaurer cette dernière telle qu’elle était au moment de la suppression. En effet, avant qu’une base de données ne soit supprimée, Azure SQL Data Warehouse en effectue une capture instantanée et la conserve pendant 7 jours.

### PowerShell
Utilisez Azure PowerShell pour exécuter par programme la restauration d’une base de données supprimée. Pour télécharger le module Azure PowerShell, exécutez [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).

Pour restaurer une base de données supprimée, utilisez l’applet de commande [Start-AzureSqlDatabaseRestore][].

1. Ouvrez Microsoft Azure PowerShell.
2. Connectez-vous à votre compte Azure et répertoriez tous les abonnements associés à votre compte.
3. Sélectionnez l’abonnement qui contient la base de données supprimée à restaurer.
4. Dans la liste des bases de données supprimées, recherchez la base de données supprimée et sa date de suppression.

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. Accédez à la base de données supprimée et démarrez la restauration.

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Notez que si votre serveur est nommé foo.database.windows.net, vous devez utiliser « foo »comme nom du serveur dans les applets de commande Powershell ci-dessus.

### API REST
Utilisez REST pour exécuter par programmation la restauration des bases de données.

1.	Obtenez la liste de toutes vos bases de données supprimées pouvant être restaurées en suivant la procédure [Liste des bases de données supprimées pouvant être restaurées][].
2.	Obtenez des informations détaillées sur la base de données que vous voulez restaurer en suivant la procédure [Obtention de base de données supprimée pouvant être restaurée][].
3.	Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données][].
4.	Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données][].

Une fois la restauration terminée, vous pouvez configurer la base de données récupérée à utiliser en suivant le guide [Finaliser une base de données récupérée][].


## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité d’autres éditions de Base de données SQL Azure, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure][].


<!--Image references-->

<!--Article references-->
[Vue d’ensemble de la continuité des activités de la base de données SQL Azure]: sql-database/sql-database-business-continuity.md
[Finaliser une base de données récupérée]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Création d’une demande de restauration de base de données]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Statut d’opération de base de données]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Obtention de base de données supprimée pouvant être restaurée]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[Liste des bases de données supprimées pouvant être restaurées]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/fr-FR/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=Oct15_HO3-->