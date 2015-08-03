<properties 
   pageTitle="Récupération des erreurs des utilisateurs de bases de données SQL" 
   description="Découvrez comment récupérer le système après une erreur d’un utilisateur, une altération accidentelle des données ou une base de données supprimée à l’aide de la fonctionnalité de limite de restauration dans le temps de la base de données SQL Microsoft Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="07/14/2015"
   ms.author="elfish"/>

# Récupération d’une base de données SQL Microsoft Azure suite à une erreur de l’utilisateur

La base de données SQL Microsoft Azure offre deux fonctionnalités essentielles pour effectuer une récupération suite à une erreur de l’utilisateur ou à la modification involontaire de données.

- Limite de restauration dans le temps 
- Restauration d’une base de données supprimée

Pour en savoir plus sur ces fonctionnalités, reportez-vous à ce [billet de blog](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/).

La base de données SQL Azure effectue toujours les restaurations vers une nouvelle base de données. Ces fonctions de restauration sont proposées pour toutes les bases de données, quel que soit leur niveau (niveau de base, Standard et Premium).
##Récupération via la limite de restauration dans le temps
En cas d’erreur de l’utilisateur ou de modification involontaire des données, vous pouvez utiliser la fonction de limite de restauration dans le temps pour restaurer une base de données à un moment donné, inclus dans la période de rétention prévue pour vos bases de données.

Les bases de données de niveau de base proposent une période de rétention de 7 jours. Quant aux niveaux Standard et Premium, ils offrent des périodes de rétention de 14 et 35 jours, respectivement. Pour en savoir plus sur le mécanisme de rétention de base de données, consultez notre [vue d’ensemble de la continuité d’activité](sql-database-business-continuity.md).

###Portail Azure
1. Connectez-vous au [portail Microsoft Azure](https://portal.Azure.com).
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Bases de données SQL**
3. Accédez à votre base de données et sélectionnez-la.
4. Sur la partie supérieure du volet de votre base de données, sélectionnez **Restaurer**.
5. Indiquez un nom de base de données et un moment donné, puis cliquez sur **Créer**.
6. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**, sur le côté gauche de l’écran.

Une fois la restauration terminée, vous pouvez configurer la base de données récupérée à utiliser, en suivant le guide de [finalisation d’une base de données récupérée](sql-database-recovered-finalize.md).
###PowerShell
Utilisez PowerShell pour exécuter par programmation la restauration d’une base de données.

Pour restaurer une base de données avec la fonction de limite de restauration dans le temps, utilisez l’applet de commande [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Pour obtenir les étapes détaillées à suivre, consultez notre [vidéo de procédure](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/).

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
Une fois la restauration terminée, vous pouvez configurer la base de données récupérée à utiliser, en suivant le guide de [finalisation d’une base de données récupérée](sql-database-recovered-finalize.md).

###API REST 
Utilisez REST pour exécuter par programmation la restauration des bases de données.

1. Obtenez la base de données à restaurer en suivant la procédure d’[obtention de la base de données](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Créez la demande de restauration en suivant la procédure de [création d’une demande de restauration de base de données](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Suivez la demande de restauration en suivant la procédure de suivi de l’[état du fonctionnement de la base de données](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Une fois la restauration terminée, vous pouvez configurer la base de données récupérée à utiliser, en suivant le guide de [finalisation d’une base de données récupérée](sql-database-recovered-finalize.md).

##Récupérer une base de données supprimée
En cas de suppression d’une base de données, la base de données SQL Microsoft Azure permet de la restaurer au moment de cette suppression. La base de données SQL Microsoft Azure stocke la sauvegarde de base de données supprimée pendant la période de rétention de la base de données.

La période de rétention d’une base de données supprimée est déterminée par le niveau de service de la base de données pendant sa période d’existence, ou le nombre de jours d’existence de la base de données. La valeur la plus faible est choisie. Pour en savoir plus sur la rétention de base de données, consultez notre [vue d’ensemble de la fonction de continuité d’activité](sql-database-business-continuity.md).

###Portail Azure
1. Connectez-vous au [portail Microsoft Azure](https://portal.Azure.com).
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Serveur SQL Server**.
3. Accédez à votre serveur et sélectionnez-le.
4. Sous l’option **Opérations** figurant sur le panneau du serveur, sélectionnez **Bases de données supprimées**.
5. Sélectionnez la base de données supprimée que vous souhaitez restaurer.
6. Spécifiez un nom de base de données, puis cliquez sur **Créer**.
7. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**, sur le côté gauche de l’écran.

Une fois la restauration terminée, vous pouvez configurer la base de données récupérée à utiliser, en suivant le guide de [finalisation d’une base de données récupérée](sql-database-recovered-finalize.md).

###PowerShell
Utilisez PowerShell pour exécuter par programmation la restauration d’une base de données.

Pour restaurer une base de données supprimée, utilisez l’applet de commande [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Pour obtenir les étapes détaillées à suivre, consultez notre [vidéo de procédure](http://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/).

1. Dans la liste des bases de données supprimées, recherchez la base de données supprimée et sa date de suppression.
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. Accédez à la base de données supprimée et démarrez la restauration.

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
Une fois la restauration terminée, vous pouvez configurer la base de données récupérée à utiliser, en suivant le guide de [finalisation d’une base de données récupérée](sql-database-recovered-finalize.md).

###API REST 
Utilisez REST pour exécuter par programmation la restauration des bases de données.

1.	Obtenez la liste de toutes vos bases de données supprimées pouvant être restaurées en suivant la procédure de création de [listes des bases de données supprimées pouvant être restaurées](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Obtenez des informations détaillées sur la base de données que vous voulez restaurer en suivant la procédure d’[obtention de base de données supprimée pouvant être restaurée](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Lancez la restauration en suivant la procédure de [création d’une demande de restauration de base de données](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Surveillez l’état de la restauration en suivant la procédure de suivi de l’[état de fonctionnement de base de données](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Une fois la restauration terminée, vous pouvez configurer la base de données récupérée à utiliser, en suivant le guide de [finalisation d’une base de données récupérée](sql-database-recovered-finalize.md).
 

<!---HONumber=July15_HO4-->