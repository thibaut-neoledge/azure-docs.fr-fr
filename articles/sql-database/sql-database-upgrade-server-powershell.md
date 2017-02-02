---
title: "Mettre à niveau vers Azure SQL Database V12 à l’aide de PowerShell | Microsoft Docs"
description: "Explique comment effectuer une mise à niveau vers Azure SQL Database V12, notamment la mise à niveau des bases de données Web et Business et la mise à niveau d’un serveur V11 migrant ses bases de données directement dans un pool élastique à l’aide de PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: bb87b726-496c-4429-a43a-103a3e89abc4
ms.service: sql-database
ms.custom: V11
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: ae40c24c90a22f3f57256f06313423d7bb152a1a


---
# <a name="upgrade-to-azure-sql-database-v12-using-powershell"></a>Mise à niveau vers Azure SQL Database V12 à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Portail Azure](sql-database-upgrade-server-portal.md)
> * [PowerShell](sql-database-upgrade-server-powershell.md)
> 
> 

SQL Database V12 est la version la plus récente. Il est donc recommandé d’effectuer une mise à niveau vers SQL Database V12. Pour une matrice de fonctionnalité comparant Azure SQL Database avec SQL Server, voir [Fonctionnalités Azure SQL Database](sql-database-features.md).

Cet article fournit des instructions sur la mise à niveau des serveurs et bases de données SQL Database V11 existants vers SQL Database V12.

Lors du processus de mise à niveau vers la version V12, vous mettez à niveau toutes les bases de données Web et Business vers un nouveau niveau de service. C’est pourquoi des instructions pour la mise à niveau des bases de données Web et Business sont incluses.

En outre, la migration vers un [pool élastique](sql-database-elastic-pool.md) peut être plus économique que la mise à niveau vers des niveaux de performances individuels (niveaux tarifaires) pour des bases de données autonomes. Les pools simplifient également la gestion des bases de données, car vous devez uniquement gérer les paramètres de performances du pool, et non gérer séparément les niveaux de performances des bases de données individuelles. Si vous disposez de bases de données sur plusieurs serveurs, envisagez de les déplacer dans le même serveur et de tirer parti de leur regroupement au sein d’un pool.

Vous pouvez suivre les étapes décrites dans cet article pour migrer facilement des bases de données à partir de serveurs V11 directement dans des pools élastiques.

Notez que vos bases de données resteront en ligne et continueront à fonctionner pendant toute la durée de l’opération de mise à niveau. Au moment précis de la transition vers le nouveau niveau de performances, une perte temporaire des connexions à la base de données peut parfois se produire pendant une très courte durée, généralement pendant environ 90 secondes, mais cette durée peut atteindre 5 minutes. Si votre application [gère les problèmes temporaires d’interruption de connexion](sql-database-connectivity-issues.md) , il suffit d’établir une protection contre la perte de connexion à la fin de la mise à niveau.

Il n’est pas possible d’annuler la mise à niveau vers SQL Database V12. Après une mise à niveau, le serveur ne peut pas être restauré vers la version V11.

Après la mise à niveau vers la version V12, les [recommandations du niveau de service](sql-database-service-tier-advisor.md) et les [recommandations du pool élastique](sql-database-elastic-pool-create-portal.md) ne sont pas disponibles immédiatement. Elles le seront lorsque le service aura eu le temps d’évaluer vos charges de travail sur le nouveau serveur. L’historique des recommandations du serveur V11 ne s’applique pas aux serveurs V12 et n’est donc pas conservé.  

## <a name="prepare-to-upgrade"></a>Préparation de la mise à niveau
* **Mise à niveau de toutes les bases de données Web et Business** : utilisez le portail ou [PowerShell pour mettre à niveau les bases de données et le serveur](sql-database-upgrade-server-powershell.md).
* **Vérifier et suspendre la géoréplicationréplication**: si votre base de données SQL Azure est configurée pour la géoréplication, vous devez documenter la configuration actuelle et [arrêter la géoréplication](sql-database-geo-replication-portal.md#remove-secondary-database). Une fois la mise à niveau terminée, reconfigurez votre base de données pour la géoréplication.
* **Ouvrez ces ports si vous avez des clients sur une machine virtuelle Azure**: si votre programme client se connecte à SQL Database V12 pendant que votre client s’exécute sur une machine virtuelle Azure, vous devez ouvrir les plages de ports 11000-11999 et 14000-14999 sur la machine virtuelle. Pour plus d'informations, consultez la rubrique [Ports pour SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="prerequisites"></a>Composants requis
Pour mettre à niveau un serveur vers V12 avec PowerShell, la dernière version d’Azure PowerShell doit être installée et en cours d’exécution. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurer vos informations d’identification et sélectionner votre abonnement
Pour exécuter les applets de commande PowerShell sur votre abonnement Azure, vous devez d’abord établir l’accès à votre compte Azure. Exécutez la commande suivante ; un écran de connexion s’affichera, dans lequel vous pourrez entrer vos informations d’identification. Utilisez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

    Add-AzureRmAccount

Après vous être connecté, des informations s'affichent sur l'écran, notamment l'ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.

Pour sélectionner l’abonnement que vous souhaitez utiliser, vous avez besoin de votre identifiant (**-SubscriptionId**) ou de votre nom d’abonnement (**-SubscriptionName**). Vous pouvez le copier à partir de l’étape précédente ou, si vous avez plusieurs abonnements, vous pouvez exécuter l’applet de commande **Get-AzureRMSubscription** et copier les informations d’abonnement souhaitées affichées dans les résultats.

Exécuter l’applet de commande suivant avec vos informations d’abonnement pour définir votre abonnement actuel :

    Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Les applets de commande suivant seront exécutés sur l’abonnement sélectionné à l’étape précédente.

## <a name="get-recommendations"></a>Obtention de recommandations
Pour obtenir la recommandation relative à la mise à niveau du serveur, exécutez l'applet de commande suivante :

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Pour plus d’informations, consultez les pages [Créer un pool élastique](sql-database-elastic-pool-create-portal.md) et [Recommandations relatives aux niveaux tarifaires Azure SQL Database](sql-database-service-tier-advisor.md).

## <a name="start-the-upgrade"></a>Lancer la mise à niveau
Pour lancer la mise à niveau du serveur, exécutez l’applet de commande suivante :

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Lorsque vous exécutez cette commande, le processus de mise à niveau commence. Vous pouvez personnaliser la sortie de la recommandation et fournir les recommandations modifiées à cette applet de commande.

## <a name="upgrade-a-server"></a>Mise à niveau d’un serveur
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
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## <a name="custom-upgrade-mapping"></a>Mappage de la mise à niveau personnalisée
Si les recommandations ne sont pas adaptées à votre serveur et à votre situation, vous pouvez choisir la façon dont vos bases de données sont mises à niveau et les mapper à des bases de données autonomes ou des pools élastiques.

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

    # Creating standalone database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
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
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Surveillance des bases de données après la mise à niveau vers SQL Database V12
Après la mise à niveau, nous vous recommandons de surveiller activement la base de données pour vous assurer que les applications s’exécutent au niveau de performances souhaité et pour optimiser l’utilisation en fonction des besoins.

Outre les bases de données individuelles, vous pouvez surveiller les pools élastiques [à l’aide du portail](sql-database-elastic-pool-manage-portal.md) ou de [PowerShell](sql-database-elastic-pool-manage-powershell.md).

**Données sur la consommation de ressources** : pour les bases de données De base, Standard et Premium, des données sur la consommation des ressources sont disponibles par le biais de la vue de gestion dynamique (DMV)[sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) dans la base de données utilisateur. Cette vue offre presque en temps réel les informations de consommation des ressources avec un niveau de granularité de 15 secondes pour l’heure de fonctionnement précédente. La consommation de pourcentage DTU pour un intervalle est calculée comme la consommation de pourcentage maximal des dimensions UC, E/S et journal. Voici une requête pour calculer la moyenne de pourcentage DTU sur la dernière heure :

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informations de surveillance supplémentaires :

* [Guide des performances Azure SQL Database pour les bases de données autonomes](http://msdn.microsoft.com/library/azure/dn369873.aspx).
* [Considérations sur les prix et performances pour un pool élastique](sql-database-elastic-pool-guidance.md).
* [Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)

**Alertes :** configurez les « alertes » dans le portail Azure pour vous avertir quand la consommation DTU d’une base de données mise à niveau approche d’un niveau élevé. Les alertes de la base de données peuvent être configurées dans le Portail Azure pour diverses mesures de performances comme DTU, UC, E/S et journal. Accédez à votre base de données et sélectionnez **Règles d’alerte** dans le panneau **Paramètres**.

Par exemple, vous pouvez configurer une alerte par courrier électronique sur « Pourcentage DTU » si la valeur moyenne du pourcentage DTU est supérieure à 75 % pendant les 5 dernières minutes. Reportez-vous à [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) pour en savoir plus sur la configuration des notifications d’alerte.

## <a name="next-steps"></a>Étapes suivantes
* [Créer un pool élastique](sql-database-elastic-pool-create-portal.md) et ajouter quelques bases de données, ou toutes, dans le pool.
* [Modifier les niveaux de service et de performances de votre base de données](sql-database-scale-up.md).

## <a name="related-information"></a>Informations connexes
* [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
* [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
* [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)




<!--HONumber=Dec16_HO2-->


