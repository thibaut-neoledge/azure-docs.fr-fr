<properties 
   pageTitle="Récupération d’urgence de bases de données SQL" 
   description="Découvrez comment récupérer une base de données en cas de panne d’un centre de données régional grâce aux fonctionnalités de géo-réplication active de base de données SQL Azure, de géo-réplication standard et de restauration géographique." 
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
   ms.date="11/09/2015"
   ms.author="elfish"/>

# Récupérer une base de données SQL Azure en cas de défaillance

La base de données SQL Azure offre les fonctionnalités suivantes pour la récupération après une panne :

- activation de la réplication géographique [(blog)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- réplication géographique standard [(blog)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- Géo-restauration [(blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)
- Nouvelles fonctionnalités de géo-réplication [(blog)](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)

Pour en savoir plus sur la préparation aux sinistres et découvrir quand récupérer votre base de données, visitez notre page [Conception pour la continuité des activités](sql-database-business-continuity-design.md).

##Quand initier la récupération ? 

L'opération de récupération a un impact sur l'application. Elle requiert la modification de la chaîne de connexion SQL et peut entraîner une perte de données définitive. Par conséquent, elle doit être effectuée uniquement lorsque la défaillance est susceptible de durer plus longtemps que le RTO de votre application. Lorsque l'application est déployée en production, vous devez effectuer une surveillance régulière de l'intégrité de l'application et vous assurer des points suivants pour déclarer que la récupération est garantie :

1. Échec permanent de la connectivité de la couche d'application à la base de données.
2. Votre portail Azure Classic affiche une alerte concernant un incident dans la région avec un large impact.

> [AZURE.NOTE] Une fois votre base de données récupérée, vous pouvez la configurer pour l'utiliser en suivant le guide [Configure your database after recovery](#postrecovery).

## Basculement vers la base de données secondaire géo-répliquée
> [AZURE.NOTE] Vous devez configurer une base de données secondaire à utiliser pour le basculement. La réplication géographique est uniquement disponible pour les bases de données Standard et Premium. Apprenez à [configurer la géo-réplication](sql-database-business-continuity-design.md)

###Portail Azure Classic
Utilisez le portail Azure Classic pour mettre fin à la relation de copie continue avec la base de données secondaire géo-répliquée.

1. Connectez-vous au [portail Azure Classic](https://portal.Azure.com)
2. Sur le côté gauche de l'écran, sélectionnez **PARCOURIR**, puis sélectionnez **Bases de données SQL**
3. Naviguez jusqu'à votre base de données et sélectionnez-la. 
4. En bas du panneau de votre base de données, sélectionnez le **Plan de réplication géographique**.
4. Sous **Secondaires**, cliquez avec le bouton droit sur la ligne contenant le nom de la base de données que vous souhaitez récupérer, puis sélectionnez **Failover**.

###PowerShell
Utilisez PowerShell pour lancer le basculement vers la base de données secondaire géo-répliquée à l'aide de l’applet de commande [Set-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).
		
		$database = Get-AzureRMSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
		$database | Set-AzureRMSqlDatabaseSecondary –Failover -AllowDataLoss

###API REST 
REST permet d'initialiser le basculement vers une base de données secondaire par programme.

1. Obtenez un lien de réplication vers un secondaire spécifique en utilisant l’opération [Get Replication Link](https://msdn.microsoft.com/library/mt600778.aspx).
2. Basculement vers le secondaire à l'aide de [Set Secondary Database As Primary](https://msdn.microsoft.com/library/mt582027.aspx) avec perte de données autorisée. 

## Récupération à l’aide de la géo-restauration

En cas de défaillance d'une base de données, vous pouvez récupérer votre base de données à partir de sa dernière sauvegarde géographique redondante à l'aide de la restauration géographique.

> [AZURE.NOTE] La récupération d'une base de données crée une nouvelle base de données. Il est important de s'assurer que le serveur sur lequel vous effectuez la récupération a une capacité DTU suffisante pour la nouvelle base de données. Vous pouvez demander une augmentation de ce quota en [contactant le support](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/).

###Portail Azure Classic
Pour restaurer une base de données SQL à l’aide de la géo-restauration dans le portail Azure Classic, procédez comme suit.

1. Connectez-vous au [portail Azure Classic](https://portal.Azure.com)
2. Sur le côté gauche de l'écran, sélectionnez **NOUVEAU**, puis sélectionnez **Données et stockage** et **Base de données SQL**.
2. Sélectionnez **SAUVEGARDE** comme source, puis sélectionnez la sauvegarde géographique redondante à partir de laquelle vous souhaitez effectuer la récupération.
3. Spécifiez le reste des propriétés de base de données, puis cliquez sur **Créer**.
4. Le processus de restauration de base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**, sur le côté gauche de l’écran.

###PowerShell 
Pour restaurer une base de données SQL à l’aide de la géo-restauration avec PowerShell, démarrez une demande de géo-restauration avec l’applet de commande [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

###API REST 

Utilisez REST pour exécuter la récupération de base de données par programmation.

1.	Obtenez la liste des bases de données récupérables à l'aide de l'opération [List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Obtenez la base de données à récupérer à l'aide de l'opération [Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Créez la requête de récupération à l'aide de l'opération [Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Suivez l’état de la récupération à l’aide de l’opération [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).
 
## Configuration de votre base de données après restauration<a name="postrecovery"></a>

Voici une liste de contrôle des tâches pouvant être utilisée pour vous aider à préparer votre base de données de production pour la restauration.

### Mettre à jour les chaînes de connexion

Vérifiez que les chaînes de connexion de votre application pointent bien vers la base de données qui vient d’être restaurée. Mettez vos chaînes de connexion à jour dans l’un des cas suivants :

  + La base de données restaurée utilise un nom différent de celui de la base de données source.
  + La base de données restaurée se trouve sur un serveur différent du serveur source.

Pour en savoir plus sur la modification des chaînes de connexion, consultez [Connexion à la base de données SQL : recommandations principales](sql-database-connect-central-recommendations.md).
 
### Modifier les règles de pare-feu
Vérifiez les règles de pare-feu appliquées au niveau du serveur et de la base de données. Assurez-vous également que les connexions entre les ordinateurs clients et le serveur et la base de données qui vient d’être restaurée sont activées. Pour plus d’informations, voir [Procédure : configuration des paramètres du pare-feu (Base de données SQL Azure)](sql-database-configure-firewall-settings.md).

### Vérifier les connexions de serveur et les utilisateurs des bases de données

Vérifiez que toutes les connexions utilisées par votre application existent sur le serveur qui héberge votre base de données restaurée. Créez à nouveau les connexions manquantes et accordez-leur les autorisations appropriées sur la base de données restaurée. Pour en savoir plus, voir [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure](sql-database-manage-logins.md).

Vérifiez que les utilisateurs de chaque base de données, dans la base de données restaurée, sont bien associés à une connexion de serveur valide. Utilisez l’instruction ALTER USER pour mapper l’utilisateur à une connexion de serveur valide. Pour en savoir plus, voir [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486).


### Configurer les alertes de télémétrie

Vérifiez que les paramètres de vos règles d’alertes existantes sont mappés à la base de données restaurée. Mettez à jour le paramètre dans l’un des cas suivants :

  + La base de données restaurée utilise un nom différent de celui de la base de données source.
  + La base de données restaurée se trouve sur un serveur différent du serveur source.

Pour en savoir plus, voir [Réception de notifications d'alerte](insights-receive-alert-notifications.md) et [Suivi de l’intégrité du service](insights-service-health.md).


### Activer la fonction d’audit

Si la fonction d’audit doit accéder à votre base de données, vous devez l’activer après la restauration de la base de données. Un bon indicateur de la nécessité d’activer l’audit est l’utilisation, par les applicatives clientes, de chaînes de connexion sécurisées dans un modèle *.database.secure.windows.net. Pour en savoir plus, voir [Prise en main de l’audit de base de données SQL](sql-database-auditing-get-started.md).

<!---HONumber=AcomDC_0128_2016-->