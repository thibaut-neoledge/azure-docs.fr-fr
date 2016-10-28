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
    ms.date="08/19/2016"
    ms.author="sstein"/>

# Créer une base de données SQL et effectuer des tâches courantes d’installation de base de données avec les applets de commande PowerShell


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Découvrez comment créer une base de données SQL avec des applets de commande PowerShell. (Pour la création de bases de données élastiques, consultez [Créer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]

## Configuration de la base de données : Créer un groupe de ressources, un serveur et une règle de pare-feu

Une fois que vous disposez d’un accès pour exécuter des applets de commande pour votre abonnement Azure, l’étape suivante consiste à établir le groupe de ressources qui contient le serveur où la base de données sera créée. Vous pouvez modifier la commande suivante pour utiliser l'emplacement valide de votre choix. Exécutez **(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location** pour obtenir la liste des emplacements autorisés.

Utilisez la commande suivante pour créer un groupe de ressources :

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### Créer un serveur

Les bases de données SQL sont créées dans les serveurs du service Base de données SQL Azure. Exécutez **New-AzureRMSqlServer** pour créer un serveur. Le nom du serveur doit être unique pour tous les serveurs du service Base de données SQL Azure. Vous obtiendrez une erreur si le nom est déjà utilisé pour un autre serveur. Il est également à noter que l'exécution de cette commande peut prendre plusieurs minutes. Vous pouvez modifier la commande à utiliser n’importe quel emplacement valide de votre choix, mais vous devez utiliser le même emplacement que celui utilisé pour le groupe de ressources créé à l’étape précédente.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Lorsque vous exécutez cette commande, vous êtes invité à fournir votre nom d’utilisateur et votre mot de passe. N’entrez pas vos informations d’identification Azure. Au lieu de cela, entrez le nom d’utilisateur et le mot de passe à créer en tant qu’administrateur du serveur. Le script en bas de cet article montre comment définir les informations d’identification du serveur à l’aide d’un code.

Les détails du serveur apparaissent une fois le serveur créé.

### Configurer une règle de pare-feu de serveur pour autoriser l'accès au serveur

Vous devez établir une règle de pare-feu pour accéder au serveur. Exécutez la commande suivante, en remplaçant les adresses IP de début et de fin par des valeurs autorisées pour votre ordinateur.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Les détails de la règle de pare-feu apparaissent une fois la règle créée.

Pour autoriser d’autres services Azure à accéder à un serveur, ajoutez une règle de pare-feu, et définissez StartIpAddress et EndIpAddress sur 0.0.0.0. Cette configuration autorise le trafic Azure à accéder au serveur depuis n’importe quel abonnement Azure.

Pour en savoir plus, consultez [Pare-feu de la base de données SQL Azure](sql-database-firewall-configure.md).


## Créer une base de données SQL

Vous disposez maintenant d'un groupe de ressources, d'un serveur et d'une règle de pare-feu configurés pour vous permettre un accès au serveur.

La commande suivante permet de créer une base de données SQL (vide) au niveau de service Standard avec un niveau de performances S1 :


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Les détails de la base de données apparaissent une fois la base de données créée.

## Créer un script PowerShell de base de données SQL

Le script PowerShell suivant crée une base de données SQL et toutes ses ressources dépendantes. Remplacez tout `{variables}` par des valeurs spécifiques à votre abonnement et ressources (supprimez les **{}** lorsque vous définissez les valeurs).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## Étapes suivantes
Une fois que vous avez créé une base de données SQL et effectué les tâches courantes d’installation de base de données, vous êtes prêt pour les opérations suivantes :

- [Gérer la base de données SQL avec PowerShell](sql-database-manage-powershell.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)


## Ressources supplémentaires

- [Base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1005_2016-->