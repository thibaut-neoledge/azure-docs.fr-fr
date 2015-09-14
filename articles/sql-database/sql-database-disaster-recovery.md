<properties 
   pageTitle="Récupération d'urgence de bases de données SQL"
	description="Découvrez comment récupérer une base de données en cas de panne d'un centre de données régional grâce aux fonctionnalités de géo-réplication de base de données SQL Azure et de restauration géographique."
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

# Récupérer une base de données SQL Azure en cas de défaillance

La base de données Azure SQL offre quelques fonctionnalités de récupération en cas de défaillance :

- activation de la réplication géographique [(blog)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- réplication géographique standard [(blog)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- restauration géographique [(blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

Pour en savoir plus sur la préparation aux sinistres et découvrir quand restaurer votre base de données, visitez notre page [Conception pour la continuité des activités](sql-database-business-continuity-design.md).

##Quand initier la récupération ? 

L'opération de récupération a un impact sur l'application. Elle requiert la modification de la chaîne de connexion SQL et peut entraîner une perte de données définitive. Par conséquent, elle doit être effectuée uniquement lorsque la défaillance est susceptible de durer plus longtemps que le RTO de votre application. Lorsque l'application est déployée en production, vous devez effectuer une surveillance régulière de l'intégrité de l'application et vous assurer des points suivants pour déclarer que la récupération est garantie :

1. Échec permanent de la connectivité de la couche d'application à la base de données.
2. Votre portail Azure affiche une alerte concernant un incident dans la région avec un large impact.

## Basculement vers la base de données secondaire géo-répliquée
> [AZURE.NOTE]Vous devez configurer la [Réplication géographique standard](https://msdn.microsoft.com/library/azure/dn758204.aspx) ou la [Réplication géographique active](https://msdn.microsoft.com/library/azure/dn741339.aspx) pour disposer d'une base de données secondaire à utiliser pour le basculement. La réplication géographique est uniquement disponible pour les bases de données Standard et Premium.

En cas de panne sur la base de données primaire, vous pouvez basculer vers une base de données secondaire pour restaurer la disponibilité. Pour cela, vous devez forcer la terminaison de la relation de copie continue. Pour une description complète de la terminaison de relations de copie continues, allez [ici](https://msdn.microsoft.com/library/azure/dn741323.aspx).



###Portail Azure
1. Connectez-vous au [portail Azure](https://portal.Azure.com).
2. Sur le côté gauche de l'écran, sélectionnez **PARCOURIR**, puis sélectionnez **Bases de données SQL**
3. Naviguez jusqu'à votre base de données et sélectionnez-la. 
4. En bas du panneau de votre base de données, sélectionnez le **Plan de réplication géographique**.
4. Sous **Secondaires**, cliquez avec le bouton droit sur la ligne contenant le nom de la base de données que vous souhaitez récupérer, puis sélectionnez **Arrêter**.

Une fois la relation de copie continue interrompue, vous pouvez configurer la base de données récupérée pour l'utiliser en suivant le guide [Finaliser une base de données récupérée](sql-database-recovered-finalize.md).
###PowerShell
Utilisez PowerShell pour exécuter la récupération de base de données par programmation.

Pour interrompre la relation à partir de la base de données secondaire, utilisez l'applet de commande [Stop-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720223).
		
		$myDbCopy = Get-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName"
		$myDbCopy | Stop-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -ForcedTermination
		 
Une fois la relation de copie continue interrompue, vous pouvez configurer la base de données récupérée pour l'utiliser en suivant le guide [Finaliser une base de données récupérée](sql-database-recovered-finalize.md).
###API REST 
Utilisez REST pour exécuter la récupération de base de données par programmation.

1. Obtenez la copie de base de données continue à l'aide de l'opération [Get Database Copy](https://msdn.microsoft.com/library/azure/dn509570.aspx).
2. Arrêtez la copie de base de données à l'aide de l'opération [Stop Database Copy](https://msdn.microsoft.com/library/azure/dn509573.aspx). Utilisez le nom du serveur secondaire et le nom de la base de données dans l'URI de la requête Stop Database Copy.

 Une fois la relation de copie continue interrompue, vous pouvez configurer la base de données récupérée pour l'utiliser en suivant le guide [Finaliser une base de données récupérée](sql-database-recovered-finalize.md).
## Récupération à l'aide de la restauration géographique

En cas de défaillance d'une base de données, vous pouvez récupérer votre base de données à partir de sa dernière sauvegarde géographique redondante à l'aide de la restauration géographique.

> [AZURE.NOTE]La récupération d'une base de données crée une nouvelle base de données. Il est important de s'assurer que le serveur sur lequel vous effectuez la récupération a une capacité DTU suffisante pour la nouvelle base de données. Vous pouvez demander une augmentation de ce quota en [contactant le support](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portail Azure
1. Connectez-vous au [portail Azure](https://portal.Azure.com).
2. Sur le côté gauche de l'écran, sélectionnez **NOUVEAU**, puis sélectionnez **Données et stockage** et **Base de données SQL**.
2. Sélectionnez **SAUVEGARDE** comme source, puis sélectionnez la sauvegarde géographique redondante à partir de laquelle vous souhaitez effectuer la récupération.
3. Spécifiez le reste des propriétés de base de données, puis cliquez sur **Créer**.
4. Le processus de restauration de base de données commence et peut surveillé à l'aide du volet **NOTIFICATIONS** sur le côté gauche de l'écran.

Une fois la base de données récupérée, vous pouvez la configurer pour l'utiliser en suivant le guide [Finaliser une base de données récupérée](sql-database-recovered-finalize.md).
###PowerShell 
Utilisez PowerShell pour exécuter la récupération de base de données par programmation.

Pour démarrer une requête de restauration géographique, utilisez l'applet de commande [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx). Pour plus de détails, regardez notre [vidéo de procédure](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell/).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

Une fois la base de données récupérée, vous pouvez la configurer pour l'utiliser en suivant le guide [Finaliser une base de données récupérée](sql-database-recovered-finalize.md).
###API REST 

Utilisez REST pour exécuter la récupération de base de données par programmation.

1.	Obtenez la liste des bases de données récupérables à l'aide de l'opération [List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Obtenez la base de données à récupérer à l'aide de l'opération [Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Créez la requête de récupération à l'aide de l'opération [Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Suivez l'état de la récupération à l'aide de l'opération [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Une fois la base de données récupérée, vous pouvez la configurer pour l'utiliser en suivant le guide [Finaliser une base de données récupérée](sql-database-recovered-finalize.md).
 

<!---HONumber=September15_HO1-->