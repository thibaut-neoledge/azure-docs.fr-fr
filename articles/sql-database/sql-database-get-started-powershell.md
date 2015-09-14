<properties 
    pageTitle="Créer une base de données SQL Azure à l’aide de PowerShell"
	description="Créer une base de données SQL Azure à l’aide de PowerShell"
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
	ms.date="09/01/2015"
	ms.author="sstein"/>

# Créer une base de données SQL à l’aide de PowerShell

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


## Vue d’ensemble

Cet article vous montre comment créer une base de données SQL à l’aide de PowerShell.


Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Azure PowerShell. Vous pouvez télécharger et installer les modules Azure PowerShell en exécutant [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).

Les applets de commande pour créer et gérer des bases de données SQL Azure sont situés dans le module Azure Resource Manager. Si vous démarrez Azure PowerShell, les applets de commande du module Azure sont importées par défaut. Pour passer au module Azure Resource Manager, utilisez l'applet de commande Switch-AzureMode.

	Switch-AzureMode -Name AzureResourceManager

Si vous exécutez l’applet de commande **Switch-AzureMode** et que vous recevez l’avertissement suivant : « *L’applet de commande Switch-AzureMode est déconseillée et sera supprimée dans une version future* », ce n’est pas un problème : accédez simplement à l’étape suivante pour configurer vos informations d’identification.

Pour plus d'informations, consultez [Utilisation de Windows PowerShell avec Resource Manager](powershell-azure-resource-manager.md).


## Configurer vos informations d'identification et sélectionner votre abonnement

Maintenant que vous exécutez le module Azure Resource Manager, vous avez accès à toutes les applets de commande nécessaires pour créer une base de données SQL.

Vous devez d’abord établir l’accès à votre compte Azure. Exécutez donc l’applet de commande suivante et vous verrez un écran de connexion pour entrer vos informations d’identification. Utilisez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Add-AzureAccount

Après vous être connecté, vous voyez des informations sur l’écran, notamment l’ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


### Sélectionner votre abonnement Azure

Pour sélectionner l’abonnement, vous avez besoin de votre ID d’abonnement. Vous pouvez le copier à partir de l'étape précédente, ou, si vous avez plusieurs abonnements, vous pouvez exécuter l'applet de commande **Get-AzureSubscription** et copier les informations d'abonnement souhaitées affichées dans les résultats. Une fois votre abonnement sélectionné, exécutez l'applet de commande suivante :

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Après avoir exécuté **Select-AzureSubscription**, vous revenez à l’invite PowerShell. Si vous avez plusieurs abonnements, vous pouvez exécuter **Get-AzureSubscription** et vérifier que l’abonnement que vous voulez utiliser affiche **IsCurrent: True**.

## Créer un groupe de ressources, un serveur et une règle de pare-feu

Vous disposez maintenant d’un accès pour exécuter des applets de commande pour votre abonnement Azure. L’étape suivante consiste donc à établir le groupe de ressources qui contient le serveur où la base de données sera créée. Vous pouvez modifier la commande suivante pour utiliser l'emplacement valide de votre choix. Exécutez **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** pour obtenir la liste des emplacements autorisés.

Utilisez la commande suivante pour créer un groupe de ressources :

	New-AzureResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Après avoir créé le nouveau groupe de ressources, vous voyez des informations sur l’écran, notamment **ProvisioningState : Opération réussie**.


### Créer un serveur 

Les bases de données SQL sont créées dans les serveurs Azure SQL Database. Exécutez **New-AzureSqlServer** pour créer un serveur. Remplacer le nom du serveur (ServerName) par le nom de votre serveur. Il doit être unique pour tous les serveurs SQL Azure. Vous recevez donc une erreur si le nom du serveur est déjà utilisé. Il est également à noter que l'exécution de cette commande peut prendre plusieurs minutes. Vous pouvez modifier la commande à utiliser n’importe quel emplacement valide de votre choix, mais vous devez utiliser le même emplacement que celui utilisé pour le groupe de ressources créé à l’étape précédente.

	New-AzureSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Lorsque vous exécutez cette commande, une fenêtre s'ouvre dans laquelle vous devez entrer un **Nom d'utilisateur** et un **Mot de passe**. Il ne s'agit pas de vos informations d'identification Azure. Entrez le nom d'utilisateur et le mot de passe qui seront les informations d'identification d'administrateur que vous souhaitez créer pour le nouveau serveur.

Les détails du serveur apparaissent une fois le serveur créé.

### Configurer une règle de pare-feu de serveur pour autoriser l'accès au serveur

Établissez une règle de pare-feu pour accéder au serveur. Exécutez la commande suivante en remplaçant les adresses IP de début et de fin par des valeurs autorisées pour votre ordinateur.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Les détails de la règle de pare-feu apparaissent une fois la règle créée.

Pour autoriser d’autres services Azure à accéder à un serveur, ajoutez une règle de pare-feu, et définissez StartIpAddress et EndIpAddress sur 0.0.0.0. Notez que cette configuration autorise le trafic Azure à accéder au serveur depuis n’importe quel abonnement Azure.

Pour en savoir plus, consultez [Pare-feu Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Créer une base de données SQL

Vous disposez maintenant d'un groupe de ressources, d'un serveur et d'une règle de pare-feu configurés pour vous permettre un accès au serveur.

La commande suivante crée une base de données SQL (vide) au niveau de service Standard avec un niveau de performance S1 :


	New-AzureSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


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
    
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Étapes suivantes

- [Se connecter avec SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)


## Ressources supplémentaires

- [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=September15_HO1-->