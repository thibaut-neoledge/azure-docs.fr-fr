<properties 
	pageTitle="Mise à niveau d’un serveur SQL Azure à la version V12 avec PowerShell" 
	description="Mettez à niveau un serveur SQL Azure à la version V12 avec PowerShell." 
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
	ms.date="10/08/2015" 
	ms.author="sstein"/>

# Mise à niveau vers la Base de données SQL V12 à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-v12-upgrade.md)
- [PowerShell](sql-database-upgrade-server.md)


Cet article vous montre comment effectuer une mise à niveau vers la Base de données SQL V12 à l’aide de PowerShell.

Au cours du processus de mise à niveau vers la Base de données SQL V12, vous devez également mettre à jour l’ensemble des bases de données Web et Business vers un nouveau niveau de service. Les instructions suivantes incluent des recommandations de niveau de tarification et de pool élastique qui facilitent la [mise à jour des bases de données Web et Business](sql-database-upgrade-new-service-tiers.md) sur le serveur.


## Composants requis 

Pour mettre à niveau un serveur à la version V12 avec PowerShell, Azure PowerShell doit être installé et en cours d’exécution. Selon la version, vous devrez peut-être passer en mode gestionnaire de ressources pour accéder aux applets de commande PowerShell Azure Resource Manager.

> [AZURE.IMPORTANT]À compter de la publication de la version préliminaire d’Azure PowerShell 1.0, l’applet de commande Switch-AzureMode n’est plus disponible, et les applets de commande présentes dans le module Azure ResourceManger ont été renommées. Les exemples de cet article utilisent les nouvelles conventions d’affectation de noms de la version préliminaire de PowerShell 1.0. Pour plus d’informations, consultez [Désapprobation de Switch-AzureMode dans Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).

Pour exécuter les applets de commande PowerShell, vous devez disposer d’Azure PowerShell. De plus, en raison de la suppression de Switch-AzureMode, vous devez télécharger et installer la dernière version d’Azure PowerShell en exécutant [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).


## Configurer vos informations d'identification et sélectionner votre abonnement

Pour exécuter les applets de commande PowerShell sur votre abonnement Azure, vous devez d’abord établir l’accès à votre compte Azure. Exécutez la commande suivante et un écran de connexion s'affichera dans lequel vous pourrez entrer vos informations d'identification. Utilisez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Add-AzureAccount

Après vous être connecté, des informations s'affichent sur l'écran, notamment l'ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.

Pour sélectionner l’abonnement que vous souhaitez utiliser, vous avez besoin de votre identifiant (**-SubscriptionId**) ou de votre nom d’abonnement (**-SubscriptionName**). Vous pouvez le copier à partir de l'étape précédente, ou, si vous avez plusieurs abonnements, vous pouvez exécuter l'applet de commande **Get-AzureSubscription** et copier les informations d'abonnement souhaitées affichées dans les résultats.

Exécuter l’applet de commande suivant avec vos informations d’abonnement pour définir votre abonnement actuel :

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Les applets de commande suivant seront exécutés sur l’abonnement sélectionné à l’étape précédente.

## Obtention de recommandations

Pour obtenir la recommandation relative à la mise à niveau du serveur, exécutez l'applet de commande suivante :

    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Pour plus d’informations, consultez les [Recommandations relatives au pool de base de données élastique SQL Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) et les [Recommandations relatives au niveau de tarification de la base de données SQL Azure](sql-database-service-tier-advisor.md).



## Lancer la mise à niveau

Pour lancer la mise à niveau du serveur, exécutez l’applet de commande suivante :

    Start-AzureRMSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Lorsque vous exécutez cette commande, le processus de mise à niveau commence. Vous pouvez personnaliser la sortie de la recommandation et fournir les recommandations modifiées à cette applet de commande.


## Mise à niveau d’un serveur


    # Adding the account
    #
    Add-AzureAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRMSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRMSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


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
    Start-AzureRMSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    




## Informations connexes

- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=Oct15_HO3-->