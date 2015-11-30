<properties 
	pageTitle="Mise à niveau vers Azure SQL Database V12 à l’aide de PowerShell | Microsoft Azure" 
	description="Explique comment effectuer une mise à niveau vers Azure SQL Database V12, notamment la mise à niveau des bases de données Web et Business et la mise à niveau d’un serveur V11 migrant ses bases de données directement dans un pool de base de données élastique à l'aide de PowerShell." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="sstein"/>

# Mise à niveau vers Azure SQL Database V12 à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Azure preview portal](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL Database V12 est la version la plus récente. Il est donc recommandé d’effectuer une mise à niveau vers SQL Database V12. SQL Database V12 dispose de nombreux [avantages par rapport à la version précédente](sql-database-v12-whats-new.md), notamment :

- Compatibilité améliorée avec SQL Server.
- Plus de performances pour le niveau Premium, nouveaux niveaux de performances.
- [Pools de base de données élastique](sql-database-elastic-pool.md).

Cet article fournit des instructions sur la mise à niveau des serveurs et bases de données SQL Database V11 existants vers SQL Database V12.

Lors du processus de mise à niveau vers la version V12, vous allez mettre à niveau toutes les bases de données Web et Business vers un nouveau niveau de service. C’est pourquoi des instructions pour la mise à niveau des bases de données Web et Business sont incluses.

En outre, la migration vers un [pool de base de données élastique](sql-database-elastic-pool.md) peut être plus économique que la mise à niveau vers des niveaux de performances individuels (niveaux de tarification) pour des bases de données uniques. Les pools simplifient également la gestion des bases de données, car vous devez uniquement gérer les paramètres de performances du pool, et non gérer séparément les niveaux de performances des bases de données individuelles. Si vous disposez de bases de données sur plusieurs serveurs, envisagez de les déplacer dans le même serveur et de tirer parti de leur regroupement au sein d’un pool.

Vous pouvez facilement auto-migrer des bases de données à partir de serveurs V11 directement dans des pools de base de données élastique en suivant les étapes décrites dans cet article.

Notez que vos bases de données resteront en ligne et continueront à fonctionner pendant toute la durée de l’opération de mise à niveau. Au moment précis de la transition vers le nouveau niveau de performances, une perte temporaire des connexions à la base de données peut parfois se produire pendant une très courte durée, généralement pendant environ 90 secondes, mais cette durée peut atteindre 5 minutes. Si votre application [gère les problèmes temporaires d’interruption de connexion](sql-database-connect-central-recommendations.md), il suffit d’établir une protection contre la perte de connexion à la fin de la mise à niveau.

Il n’est pas possible d’annuler la mise à niveau vers SQL Database V12. Après une mise à niveau, le serveur ne peut pas être restauré vers la version V11.

Après la mise à niveau vers la version V12, les [recommandations du niveau de service](sql-database-service-tier-advisor.md) et les [recommandations du pool élastique](sql-database-elastic-pool-portal.md#step-2-choose-a-pricing-tier) ne sont pas disponibles immédiatement. Elles le seront lorsque le service aura eu le temps d’évaluer vos charges de travail sur le nouveau serveur. L’historique des recommandations du serveur V11 ne s’applique pas aux serveurs V12 et n’est donc pas conservé.

## Préparation de la mise à niveau

- **Mise à niveau de toutes les bases de données Web et Business** : consultez la section [Mise à niveau de toutes les bases de données Web et Business](sql-database-v12-upgrade.md#upgrade-all-web-and-business-databases) ci-dessous ou utilisez [PowerShell pour mettre à niveau les bases de données et le serveur](sql-database-upgrade-server-powershell.md).
- **Vérifier et suspendre la géo-réplication** : si votre base de données SQL Azure est configurée pour la géo-réplication, vous devez documenter la configuration actuelle et [arrêter la géo-réplication](sql-database-geo-replication-portal.md#remove-secondary-database). Une fois la mise à niveau terminée, reconfigurez votre base de données pour la géo-réplication.
- **Ouvrez ces ports si vous avez des clients sur une machine virtuelle Azure** : si votre programme client se connecte à SQL Database V12 pendant que votre client s’exécute sur une machine virtuelle Azure, vous devez ouvrir les plages de ports 11000-11999 et 14000-14999 sur la machine virtuelle. Pour plus d'informations, consultez la rubrique [Ports pour SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Composants requis 

Pour mettre à niveau un serveur à la version V12 avec PowerShell, Azure PowerShell doit être installé et en cours d’exécution. Selon la version, vous devrez peut-être passer en mode gestionnaire de ressources pour accéder aux applets de commande PowerShell Azure Resource Manager.

> [AZURE.IMPORTANT]À compter de la publication de la version préliminaire d’Azure PowerShell 1.0, l’applet de commande Switch-AzureMode n’est plus disponible, et les applets de commande présentes dans le module Azure ResourceManger ont été renommées. Les exemples de cet article utilisent les nouvelles conventions d’affectation de noms de la version préliminaire de PowerShell 1.0. Pour plus de détails, consultez la rubrique [Désapprobation de Switch-AzureMode dans Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).

Pour exécuter les applets de commande PowerShell, vous devez disposer d’Azure PowerShell. De plus, en raison de la suppression de Switch-AzureMode, vous devez télécharger et installer la dernière version d’Azure PowerShell en exécutant [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).


## Configurer vos informations d'identification et sélectionner votre abonnement

Pour exécuter les applets de commande PowerShell sur votre abonnement Azure, vous devez d’abord établir l’accès à votre compte Azure. Exécutez la commande suivante et un écran de connexion s'affichera dans lequel vous pourrez entrer vos informations d'identification. Utilisez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Add-AzureRmAccount

Après vous être connecté, des informations s'affichent sur l'écran, notamment l'ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.

Pour sélectionner l’abonnement que vous souhaitez utiliser, vous avez besoin de votre identifiant (**-SubscriptionId**) ou de votre nom d’abonnement (**-SubscriptionName**). Vous pouvez le copier à partir de l’étape précédente ou, si vous avez plusieurs abonnements, vous pouvez exécuter l’applet de commande **Get-AzureRMSubscription** et copier les informations d’abonnement souhaitées affichées dans les résultats.

Exécuter l’applet de commande suivant avec vos informations d’abonnement pour définir votre abonnement actuel :

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Les applets de commande suivant seront exécutés sur l’abonnement sélectionné à l’étape précédente.

## Obtention de recommandations

Pour obtenir la recommandation relative à la mise à niveau du serveur, exécutez l'applet de commande suivante :

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Pour plus d’informations, consultez les [Recommandations relatives au pool de base de données élastique Azure SQL Database](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) et les [Recommandations relatives au niveau de la tarification de Azure SQL Database](sql-database-service-tier-advisor.md).



## Lancer la mise à niveau

Pour lancer la mise à niveau du serveur, exécutez l’applet de commande suivante :

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Lorsque vous exécutez cette commande, le processus de mise à niveau commence. Vous pouvez personnaliser la sortie de la recommandation et fournir les recommandations modifiées à cette applet de commande.


## Mise à niveau d’un serveur


    # Adding the account
    #
    Add-AzureRmAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Mappage de la mise à niveau personnalisée

Si les recommandations ne sont pas appropriées à votre serveur et à votre situation, vous pouvez choisir la façon dont vos bases de données sont mises à niveau et les mapper à des bases de données uniques ou élastiques.

Les paramètres ElasticPoolCollection et DatabaseCollection sont facultatifs :
    
    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties 
    $elasticPool.DatabaseDtuMax = 100 
    $elasticPool.DatabaseDtuMin = 0 
    $elasticPool.Dtu = 800 
    $elasticPool.Edition = "Standard" 
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”) 
    $elasticPool.Name = "elasticpool_1" 
    $elasticPool.StorageMb = 800 
     
    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap1.Name = "DBMain1" 
    $databaseMap1.TargetEdition = "Standard" 
    $databaseMap1.TargetServiceLevelObjective = "S0"
    
    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap2.Name = "DBMain2" 
    $databaseMap2.TargetEdition = "Standard" 
    $databaseMap2.TargetServiceLevelObjective = "S2"
     
    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    

## Surveillance des bases de données après la mise à niveau vers SQL Database V12


Après la mise à niveau, nous vous recommandons de surveiller activement la base de données pour vous assurer que les applications s’exécutent au niveau de performances souhaité et pour optimiser l’utilisation en fonction des besoins.

Outre les bases de données individuelles, vous pouvez surveiller les pools de base de données élastique [à l'aide du portail](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool) ou de [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools).


**Données sur la consommation de ressources :** pour les bases de données De base, Standard et Premium, des données sur la consommation des ressources sont disponibles via la vue de gestion dynamique (DMV) [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) dans la base de données utilisateur. Cette vue offre presque en temps réel les informations de consommation des ressources avec un niveau de granularité de 15 secondes pour l’heure précédente de l’opération. La consommation de pourcentage DTU pour un intervalle est calculée comme la consommation de pourcentage maximal des dimensions UC, E/S et journal. Voici une requête pour calculer la moyenne de pourcentage DTU sur la dernière heure :

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informations de surveillance supplémentaires :

- [Guide des performances d’Azure SQL Database pour les bases de données uniques](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considérations sur les prix et performances pour un pool de base de données élastique](sql-database=elastic-pool-guidance.md).
- [Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)



**Alertes :** configurer les « alertes » dans le portail Azure pour vous avertir quand la consommation DTU d’une base de données mise à niveau approche d’un niveau élevé. Les alertes de la base de données peuvent être configurées dans le portail Azure pour diverses mesures de performances comme DTU, UC, E/S et journal. Accédez à votre base de données et sélectionnez **Règles d’alerte** dans le panneau **Paramètres**.

Par exemple, vous pouvez configurer une alerte par courrier électronique sur « Pourcentage DTU » si la valeur moyenne du pourcentage DTU est supérieure à 75 % pendant les 5 dernières minutes. Reportez-vous à la rubrique [Réception de notifications d'alerte](insights-receive-alert-notifications.md) pour en savoir plus sur la configuration des notifications d'alerte.



## Étapes suivantes

- [Créer un pool de base de données élastique](sql-database-elastic-pool-portal.md) et ajouter quelques bases de données, ou toutes, dans le pool.
- [Modifier les niveaux de service et de performances de votre base de données](sql-database-scale-up.md).



## Informations connexes

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=Nov15_HO4-->