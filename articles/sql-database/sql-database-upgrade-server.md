<properties 
	pageTitle="Mise à niveau d’un serveur SQL Azure à la version V12 avec PowerShell" 
	description="Mise à niveau d’un serveur SQL Azure à la version V12 avec PowerShell." 
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
	ms.date="09/05/2015" 
	ms.author="sstein"/>

# Mise à niveau du serveur SQL Azure à la version V12 avec PowerShell
 

Cet article vous montre comment mettre à niveau un serveur de base de données SQL à la version V12 en utilisant les recommandations relatives au pool élastique et au niveau de tarification.



## Composants requis 

Pour mettre à niveau un serveur à la version V12 avec PowerShell, Azure PowerShell doit être installé et en cours d’exécution. Selon la version, vous devrez peut-être passer en mode gestionnaire de ressources pour accéder aux applets de commande PowerShell Azure Resource Manager.

Vous pouvez télécharger et installer les modules Azure PowerShell en exécutant [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

Les applets de commande pour créer et gérer des bases de données SQL Azure sont situés dans le module Azure Resource Manager. Si vous démarrez Azure PowerShell, les applets de commande du module Azure sont importées par défaut. Pour passer au module Azure Resource Manager, utilisez l’applet de commande **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

Si un avertissement indiquant « l’applet de commande Switch-AzureMode est déconseillé et sera supprimé dans une future version. » s’affiche, vous pouvez l’ignorer et passer à la section suivante.

Pour plus d'informations, consultez [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).



## Configurez vos informations d’identification.

Pour exécuter les applets de commande PowerShell sur votre abonnement Azure, vous devez d’abord établir l’accès à votre compte Azure. Exécutez la commande suivante et un écran de connexion s'affichera dans lequel vous pourrez entrer vos informations d'identification. Utilisez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Add-AzureAccount

Après vous être connecté, des informations s'affichent sur l'écran, notamment l'ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


## Sélectionner votre abonnement Azure

Pour sélectionner l’abonnement que vous souhaitez utiliser, vous avez besoin de votre identifiant (**-SubscriptionId**) ou de votre nom d’abonnement (**-SubscriptionName**). Vous pouvez le copier à partir de l'étape précédente, ou, si vous avez plusieurs abonnements, vous pouvez exécuter l'applet de commande **Get-AzureSubscription** et copier les informations d'abonnement souhaitées affichées dans les résultats.

Exécuter l’applet de commande suivant avec vos informations d’abonnement pour définir votre abonnement actuel :

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Les applets de commande suivant seront exécutés sur l’abonnement sélectionné à l’étape précédente.

## Obtention de recommandations

Pour obtenir la recommandation relative à la mise à niveau du serveur, exécutez l'applet de commande suivante :

    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Pour plus d'informations, consultez les [Recommandations relatives au pool de base de données élastique SQL Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) et les [Recommandations relatives au niveau de tarification de la base de données SQL Azure](sql-database-service-tier-advisor.md).



## Lancer la mise à niveau

Pour lancer la mise à niveau du serveur, exécutez l’applet de commande suivante :

    Start-AzureSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Lorsque vous exécutez cette commande, le processus de mise à niveau commence. Vous pouvez personnaliser la sortie de la recommandation et fournir les recommandations modifiées à cette applet de commande.


## Mise à niveau d'un serveur SQL Azure


    # Adding the account
    #
    Add-AzureAccount
    
    # Switch mode
    #
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Mappage de la mise à niveau personnalisée

Si les recommandations ne sont pas appropriées à votre serveur et à votre situation, vous pouvez choisir la façon dont vos bases de données sont mises à niveau et les mapper à des bases de données uniques ou élastiques.

Mise à niveau de bases de données dans un pool de base de données élastique

    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100  
    $elasticPool.DatabaseDtuMin = 0  
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"  
    $elasticPool.DatabaseCollection = ("DB1")  
    $elasticPool.Name = "elasticpool_1"  


Mise à niveau de bases de données dans des bases de données uniques :

    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties  
    $databaseMap.Name = "DB2"
    $databaseMap.TargetEdition = "Standard"
    $databaseMap.TargetServiceLevelObjective = "S0"
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool)
    




## Informations connexes

- [Applets de commande du gestionnaire de ressources Base de données SQL Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Applets de commande de gestion de Service Base de données SQL Azure](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=Sept15_HO3-->