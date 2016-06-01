<properties 
    pageTitle="Nouvelle configuration de base de données SQL avec PowerShell | Microsoft Azure" 
    description="Apprenez dès maintenant à créer une base de données SQL avec PowerShell. Les tâches courantes d’installation de base de données peuvent être gérées via les applets de commande PowerShell." 
    keywords="créer une base de données sql, configuration de base de données"
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="05/09/2016"
    ms.author="sstein"/>

# Créer une base de données SQL et effectuer des tâches courantes d’installation de base de données avec les applets de commande PowerShell 


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Découvrez comment créer une base de données SQL avec des applets de commande PowerShell. (Pour la création de bases de données élastiques, consultez [Créer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]

## Configuration de la base de données : Créer un groupe de ressources, un serveur et une règle de pare-feu

Vous disposez maintenant d’un accès pour exécuter des applets de commande pour votre abonnement Azure. L’étape suivante consiste donc à établir le groupe de ressources qui contient le serveur où la base de données sera créée. Vous pouvez modifier la commande suivante pour utiliser l'emplacement valide de votre choix. Exécutez **(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location** pour obtenir la liste des emplacements autorisés.

Utilisez la commande suivante pour créer un groupe de ressources :

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Après avoir créé le groupe de ressources, vous pouvez notamment voir la mention **ProvisioningState : Opération réussie**.


### Créer un serveur 

Les bases de données SQL sont créées dans les serveurs Azure SQL Database. Exécutez **New-AzureRMSqlServer** pour créer un serveur. Remplacer le nom du serveur (ServerName) par le nom de votre serveur. Il doit être unique pour tous les serveurs SQL Azure. Vous recevez donc une erreur si le nom du serveur est déjà utilisé. Il est également à noter que l'exécution de cette commande peut prendre plusieurs minutes. Vous pouvez modifier la commande à utiliser n’importe quel emplacement valide de votre choix, mais vous devez utiliser le même emplacement que celui utilisé pour le groupe de ressources créé à l’étape précédente.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Lorsque vous exécutez cette commande, une fenêtre s'ouvre dans laquelle vous devez entrer un **Nom d'utilisateur** et un **Mot de passe**. Il ne s'agit pas de vos informations d'identification Azure. Entrez le nom d'utilisateur et le mot de passe qui seront les informations d'identification d'administrateur que vous souhaitez créer pour le nouveau serveur.

Les détails du serveur apparaissent une fois le serveur créé.

### Configurer une règle de pare-feu de serveur pour autoriser l'accès au serveur

Établissez une règle de pare-feu pour accéder au serveur. Exécutez la commande suivante en remplaçant les adresses IP de début et de fin par des valeurs autorisées pour votre ordinateur.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Les détails de la règle de pare-feu apparaissent une fois la règle créée.

Pour autoriser d’autres services Azure à accéder à un serveur, ajoutez une règle de pare-feu, et définissez StartIpAddress et EndIpAddress sur 0.0.0.0. Notez que cette configuration autorise le trafic Azure à accéder au serveur depuis n’importe quel abonnement Azure.

Pour en savoir plus, consultez [Pare-feu de la base de données SQL Azure](sql-database-firewall-configure.md).


## Créer une base de données SQL

Vous disposez maintenant d'un groupe de ressources, d'un serveur et d'une règle de pare-feu configurés pour vous permettre un accès au serveur.

La commande suivante crée une base de données SQL (vide) au niveau de service Standard avec un niveau de performance S1 :


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Les détails de la base de données apparaissent une fois la base de données créée.

## Créer un script PowerShell de base de données SQL

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Étapes suivantes
Une fois que vous avez créé une base de données SQL et effectué les tâches courantes d’installation de base de données, vous êtes prêt pour les opérations suivantes :

- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)


## Ressources supplémentaires

- [Base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0525_2016-->