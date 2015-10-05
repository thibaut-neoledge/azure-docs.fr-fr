<properties 
   pageTitle="Créer et gérer un pool élastique de bases de données SQL à l'aide de PowerShell" 
   description="Créer et gérer un pool élastique de bases de données SQL Azure à l'aide de PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="09/23/2015"
   ms.author="adamkr; sstein"/>

# Créer et gérer un pool élastique de bases de données SQL à l'aide de PowerShell

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Vue d'ensemble

Cet article vous montre comment créer et gérer un pool élastique de bases de données SQL à l'aide de PowerShell.


Les différentes étapes de la création d'un pool élastique avec Azure PowerShell sont expliquées séparément à des fins de clarté. Pour obtenir une liste concise des commandes, consultez la section **Exemple complet** en bas de cet article.

Cet article vous explique comment créer tout ce dont vous avez besoin pour générer et configurer un pool élastique de bases de données, à l'exception de l'abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.

> [AZURE.NOTE]Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs de base de données SQL V12.


## Composants requis

Pour créer un pool élastique de bases de données avec PowerShell, vous avez besoin d'une installation d'Azure PowerShell en cours d'exécution. Basculez-la en mode Resource manager pour accéder aux applets de commande Azure Resource Manager PowerShell.

Vous pouvez télécharger et installer les modules Azure PowerShell en exécutant [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).

Les applets de commande pour créer et gérer des bases de données SQL Azure et des pools élastiques de bases de données se trouvent dans le module Azure Resource Manager. Si vous démarrez Azure PowerShell, les applets de commande du module Azure sont importées par défaut. Pour passer au module Azure Resource Manager, utilisez l'applet de commande Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Pour plus d'informations, consultez [Utilisation de Windows PowerShell avec Resource Manager](powershell-azure-resource-manager.md).


## Configurer vos informations d'identification et sélectionner votre abonnement

Maintenant que vous exécutez le module Azure Resource Manager, vous avez accès à toutes les applets de commande nécessaires pour créer et configurer un pool de bases de données élastique. Vous devez tout d'abord établir l'accès à votre compte Azure. Exécutez la commande suivante et un écran de connexion s'affichera dans lequel vous pourrez entrer vos informations d'identification. Utilisez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Add-AzureAccount

Après vous être connecté, des informations s'affichent sur l'écran, notamment l'ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


### Sélectionner votre abonnement Azure

Pour sélectionner l’abonnement, vous avez besoin de votre identifiant ou de votre nom d’abonnement (**-SubscriptionName**). Vous pouvez le copier à partir de l'étape précédente, ou, si vous avez plusieurs abonnements, vous pouvez exécuter l'applet de commande **Get-AzureSubscription** et copier les informations d'abonnement souhaitées affichées dans les résultats. Une fois votre abonnement sélectionné, exécutez l'applet de commande suivante :

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Créer un groupe de ressources, un serveur et une règle de pare-feu

Vous disposez maintenant d'un accès pour exécuter des applets de commande en fonction de votre abonnement Azure. L'étape suivante consiste donc à établir le groupe de ressources qui contient le serveur dans lequel le pool de base de données élastique est créé. Vous pouvez modifier la commande suivante pour utiliser l'emplacement valide de votre choix. Exécutez **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** pour obtenir la liste des emplacements autorisés.

Si vous disposez déjà d'un groupe de ressources, vous pouvez accéder à l’étape suivante, ou vous pouvez exécuter la commande suivante pour créer un groupe de ressources :

	New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### Créer un serveur 

Les pools élastiques de bases de données sont créés dans des serveurs Base de données SQL Azure. Si vous disposez déjà d'un serveur, vous pouvez accéder à l’étape suivante, ou vous pouvez exécuter la commande suivante pour créer un serveur V12 : Remplacer le nom du serveur (ServerName) par le nom de votre serveur. Il doit être unique pour les serveurs SQL Azure. Il se peut donc qu'une erreur soit signalée si le nom de serveur est déjà utilisé. Il est également à noter que l'exécution de cette commande peut prendre plusieurs minutes. Les détails du serveur et l'invite PowerShell apparaîtront une fois le serveur créé. Vous pouvez modifier la commande pour utiliser l'emplacement valide de votre choix.

	New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Lorsque vous exécutez cette commande, une fenêtre s'ouvre dans laquelle vous devez entrer un **Nom d'utilisateur** et un **Mot de passe**. Il ne s'agit pas de vos informations d'identification Azure. Entrez le nom d'utilisateur et le mot de passe qui seront les informations d'identification d'administrateur que vous souhaitez créer pour le nouveau serveur.


### Configurer une règle de pare-feu de serveur pour autoriser l'accès au serveur

Établissez une règle de pare-feu pour accéder au serveur. Exécutez la commande suivante en remplaçant les adresses IP de début et de fin par des valeurs autorisées pour votre ordinateur.

Si votre serveur doit autoriser l'accès à d'autres services Azure, ajoutez le commutateur **-AllowAllAzureIPs** qui insère une règle de pare-feu spéciale et autorise le trafic Azure complet à accéder au serveur.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Pour en savoir plus, consultez [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Créer un pool élastique de bases de données et des bases de données élastiques

Vous disposez maintenant d'un groupe de ressources, d'un serveur et d'une règle de pare-feu configurés pour vous permettre un accès au serveur. La commande suivante crée le pool élastique de bases de données. Cette commande crée un pool qui partage un total de 400 eDTU. Chaque base de données dans le pool a toujours 10 eDTU garanties disponibles (DatabaseDtuMin). Les différentes bases de données dans le pool peuvent consommer un maximum de 100 eDTU (DatabaseDtuMax). Pour une explication détaillée des paramètres, consultez [Pools élastiques de bases de données SQL Azure](sql-database-elastic-pool.md).


	New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Créez ou ajoutez des bases de données dans un pool élastique de bases de données

Le pool créé à l'étape précédente est vide. Il ne comporte aucune base de données élastique. Les sections suivantes indiquent comment créer des bases de données élastiques dans le pool et comment ajouter des bases de données existantes au pool.

*Après la création d’un pool, vous pouvez également utiliser Transact-SQL pour la création de nouvelles bases de données élastiques dans le pool et le déplacement de bases de données existantes dans ou hors d’un pool. Pour plus d’informations, consultez [élastique de base de pool de référence - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

### Créer une base de données élastique dans un pool élastique de bases de données

Pour créer une base de données directement à l’intérieur d’un pool, utilisez l’applet de commande **New-AzureSqlDatabase** et définissez le paramètre **ElasticPoolName**.


	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Déplacer une base de données existante vers un pool élastique de bases de données

Pour déplacer une base de données existante dans un pool, utilisez l’applet de commande **Set-AzurSqlDatabase** et définissez le paramètre **ElasticPoolName**.


À des fins d'exemple, créez une base de données qui ne se trouve pas dans un pool élastique de bases de données.

	New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Déplacez la base de données existante vers le pool de bases de données élastiques.

	Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Modifier les paramètres de performances d'un pool de bases de données élastiques


    Set-AzureSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Surveiller des bases de données et des pools élastiques de bases de données

### Obtenir l'état d'opérations de pools élastiques de bases de données

Vous pouvez suivre l’état d'opérations de pools de bases de données élastiques, notamment la création et les mises à jour.

	Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Obtenir l'état du déplacement d'une base de données élastique vers et depuis un pool élastique de bases de données

	Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Obtenir des mesures de consommation de ressources pour un pool élastique de bases de données

Mesures récupérables sous la forme d'un pourcentage de la limite de pool de ressources :

* Utilisation moyenne du processeur - cpu\_percent 
* Utilisation moyenne des E/S - data\_io\_percent 
* Utilisation moyenne du journal - log\_write\_percent 
* Utilisation moyenne de la mémoire - memory\_percent 
* Utilisation moyenne d'eDTU (en tant que valeur maximale de l'utilisation du processeur, des E/S, du journal) – DTU\_percent 
* Nombre maximum de requêtes utilisateur simultanées (travaux) – max\_concurrent\_requests 
* Nombre maximum de sessions utilisateur simultanées – max\_concurrent\_sessions 
* Taille de stockage totale pour le pool élastique – storage\_in\_megabytes 


Granularité des mesures/périodes de rétention :

* Les données seront renvoyées avec une granularité de 5 minutes.  
* La durée de conservation des données est de 14 jours.  


Cette applet de commande et API limite le nombre de lignes pouvant être récupérées au cours d'un seul appel à 1 000 (environ 3 jours de données avec une granularité de 5 minutes). Mais cette commande peut être appelée plusieurs fois avec des intervalles de temps de début / fin différents pour récupérer plus de données.


Obtenez les mesures :

	$metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Obtenez des jours supplémentaires en répétant l'appel et en ajoutant les données :

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formatez la table :

    $table = Format-MetricsAsTable $metrics 

Exportez vers un fichier CSV :

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Obtenir des mesures de consommation de ressources pour une base de données élastique

Ces API sont les mêmes que les API actuelles (V12) utilisées pour surveiller l'utilisation des ressources d'une base de données autonome, à l'exception de la différence sémantique suivante :

* Dans ce cas, les métriques d'API sont obtenues sous forme de pourcentage de DTU MAX par base de données (databaseDtuMax) (ou le nombre maximal équivalent pour la métrique sous-jacente telle que le processeur, les E/S, etc.) défini pour ce pool de bases de données élastiques. Par exemple, une utilisation de 50 % de l'une de ces métriques indique que la consommation des ressources spécifiques est de 50 % de la limite supérieure par base de données définie pour cette ressource dans le pool de bases de données élastiques parent. 

Obtenez les mesures : $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

Le cas échéant, obtenez des jours supplémentaires en répétant l'appel et en ajoutant les données :

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formatez la table :

    $table = Format-MetricsAsTable $metrics 

Exportez vers un fichier CSV :

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Exemple complet


    Switch-AzureMode -Name AzureResourceManager
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Étapes suivantes

Après avoir créé un pool élastique de bases de données, vous pouvez gérer des bases de données élastiques du pool en créant des tâches élastiques. Les tâches élastiques facilitent l'exécution de scripts T-SQL, quel que soit le nombre de bases de données dans le pool. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).


## Référence des bases de données élastiques

Pour en savoir plus sur les bases de données et les pools de bases de données élastiques, y compris les détails des API et des erreurs, consultez [Référence du pool de bases de données élastique](sql-database-elastic-pool-reference.md).

<!---HONumber=Sept15_HO4-->